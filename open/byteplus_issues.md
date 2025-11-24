import UIKit
import Foundation
import Photos
import AVFoundation
import MobileCoreServices
import UniformTypeIdentifiers

/// BytePlus EffectOne SDK Helper for iOS
/// Provides native video recording and editing capabilities
///
/// NOTE: This is a simplified iOS implementation that uses UIImagePickerController
/// For full BytePlus features (filters, effects, stickers), you need to:
/// 1. Download BytePlus iOS SDK from: https://www.volcengine.com/docs/6705/102140
/// 2. Add the framework to your Xcode project
/// 3. Follow the integration guide in: EffecOne-iOS-Sample-V1.8.0.1
/// 4. Replace this helper with proper BytePlus SDK calls
///
/// Current implementation provides:
/// ✅ Video selection from gallery (with permission handling)
/// ✅ Video recording from camera (with permission handling)
/// ✅ Video export and path return to Flutter
/// ✅ Error handling and user cancellation
///
/// To upgrade to full BytePlus SDK:
/// - Replace UIImagePickerController with BytePlus VERecorder for camera
/// - Replace UIImagePickerController with BytePlus VEEditor for gallery
/// - Add BytePlus license file (.licbag) to project
/// - Configure BytePlus auth and resources

class BytePlusHelper: NSObject {
    
    // MARK: - Singleton
    static let shared = BytePlusHelper()

    // MARK: - Properties
    private var pendingResult: FlutterResult?
    private var presentingViewController: UIViewController?
    private var imagePicker: UIImagePickerController?
    
    // MARK: - Configuration
    private let maxVideoDuration: TimeInterval = 60 * 60  // 1 hour max
    private let videoQuality: UIImagePickerController.QualityType = .typeHigh
    
    // MARK: - Initialization
    private override init() {
        super.init()
        print("🎬 [iOS BytePlus] Helper initialized")
    }
    
    func initializeSDK() {
        print("🎬 [iOS BytePlus] Initializing SDK...")

        // License file path
        guard
            let licensePath = Bundle.main.path(
                forResource: "com.volcengine.effectone.inhouse", ofType: "licbag",
                inDirectory: "License")
        else {
            print("🎬 [iOS BytePlus] ERROR: License file not found")
            return
        }

        // Authenticate
        EOLicense.checkLicense(withPath: licensePath) { [weak self] success, errMsg in
            if !success {
                print("🎬 [iOS BytePlus] Authentication failed: \(errMsg)")
            } else {
                print("🎬 [iOS BytePlus] Authentication successful, initializing resources...")
                self?.isAuthSucceeded = true

                // Initialize SDK and resources
                EOSDK.initSDK {
                    // Set resource paths
                    EOSDK.setResourceBaseDir(EOSDK.getEODocumentRootDir() + "/download")

                    // Get local bundle
                    if let bundlePath = Bundle.main.path(
                        forResource: "EOLocalResources", ofType: "bundle"),
                        let bundle = Bundle(path: bundlePath)
                    {
                        EOSDK.setResourceDefaultBuiltInConfig(
                            EOSDK.defaultPanelConfigDir(bundle.bundlePath))
                        EOSDK.setBuiltInResourceDir(EOSDK.defaultResourceDir(bundle.bundlePath))
                    }

                    // Enable remote config
                    EOSDK.useRemoteConfig(true, useRemoteResource: true)

                    print("🎬 [iOS BytePlus] SDK initialization complete")
                }
            }
        }
    }
    
    // MARK: - Public Methods

    /// Start video recorder (camera)
    /// - Parameters:
    ///   - viewController: The presenting view controller
    ///   - result: Flutter result callback
    func startRecorder(from viewController: UIViewController, result: @escaping FlutterResult) {
        print("🎬 [iOS BytePlus] Starting recorder (camera)...")

        guard UIImagePickerController.isSourceTypeAvailable(.camera) else {
            print("🎬 [iOS BytePlus] ERROR: Camera not available")
            result(
                FlutterError(
                    code: "CAMERA_UNAVAILABLE",
                    message: "Camera is not available on this device",
                    details: nil
                ))
            return
        }
        
        self.pendingResult = result
        self.presentingViewController = viewController

        // Check camera permission
        checkCameraPermission { [weak self] granted in
            if granted {
                self?.presentCamera()
            } else {
                print("🎬 [iOS BytePlus] Camera permission denied")
                result(
                    FlutterError(
                        code: "PERMISSION_DENIED",
                        message: "Camera permission denied",
                        details: nil
                    ))
                self?.cleanup()
            }
        }
    }

    /// Start editor from album (gallery)
    /// - Parameters:
    ///   - viewController: The presenting view controller
    ///   - result: Flutter result callback
    func startEditorFromAlbum(
        from viewController: UIViewController, result: @escaping FlutterResult
    ) {
        print("🎬 [iOS BytePlus] Starting editor from album (gallery)...")

        self.pendingResult = result
        self.presentingViewController = viewController

        // Check photo library permission
        checkPhotoLibraryPermission { [weak self] granted in
            if granted {
                self?.presentGallery()
            } else {
                print("🎬 [iOS BytePlus] Photo library permission denied")
                result(
                    FlutterError(
                        code: "PERMISSION_DENIED",
                        message: "Photo library permission denied. Please enable in Settings.",
                        details: nil
                    ))
                self?.cleanup()
            }
        }
    }

    // MARK: - Permission Handling

    private func checkCameraPermission(completion: @escaping (Bool) -> Void) {
        let status = AVCaptureDevice.authorizationStatus(for: .video)

        switch status {
        case .authorized:
            print("🎬 [iOS BytePlus] Camera permission already granted")
            completion(true)

        case .notDetermined:
            print("🎬 [iOS BytePlus] Requesting camera permission...")
            AVCaptureDevice.requestAccess(for: .video) { granted in
                DispatchQueue.main.async {
                    print("🎬 [iOS BytePlus] Camera permission: \(granted ? "granted" : "denied")")
                    completion(granted)
                }
            }
            
        case .denied, .restricted:
            print("🎬 [iOS BytePlus] Camera permission denied/restricted")
            DispatchQueue.main.async {
                self.showPermissionAlert(for: "Camera")
                completion(false)
            }

        @unknown default:
            print("🎬 [iOS BytePlus] Unknown camera permission status")
            completion(false)
        }
    }
    
    private func checkPhotoLibraryPermission(completion: @escaping (Bool) -> Void) {
        if #available(iOS 14, *) {
            let status = PHPhotoLibrary.authorizationStatus(for: .readWrite)

            switch status {
            case .authorized, .limited:
                print("🎬 [iOS BytePlus] Photo library permission already granted")
                completion(true)

            case .notDetermined:
                print("🎬 [iOS BytePlus] Requesting photo library permission...")
                PHPhotoLibrary.requestAuthorization(for: .readWrite) { newStatus in
                    DispatchQueue.main.async {
                        let granted = (newStatus == .authorized || newStatus == .limited)
                        print(
                            "🎬 [iOS BytePlus] Photo library permission: \(granted ? "granted" : "denied")"
                        )
                        completion(granted)
                    }
                }

            case .denied, .restricted:
                print("🎬 [iOS BytePlus] Photo library permission denied/restricted")
                DispatchQueue.main.async {
                    self.showPermissionAlert(for: "Photos")
                    completion(false)
                }

            @unknown default:
                print("🎬 [iOS BytePlus] Unknown photo library permission status")
                completion(false)
            }
        } else {
            // iOS 13 and below
            let status = PHPhotoLibrary.authorizationStatus()

            switch status {
            case .authorized:
                completion(true)

            case .notDetermined:
                PHPhotoLibrary.requestAuthorization { newStatus in
                    DispatchQueue.main.async {
                        completion(newStatus == .authorized)
                    }
                }

            case .denied, .restricted:
                DispatchQueue.main.async {
                    self.showPermissionAlert(for: "Photos")
                    completion(false)
                }

            @unknown default:
                completion(false)
            }
        }
    }
    
    private func showPermissionAlert(for feature: String) {
        guard let viewController = presentingViewController else { return }

        let alert = UIAlertController(
            title: "\(feature) Access Required",
            message: "Please enable \(feature) access in Settings to use this feature.",
            preferredStyle: .alert
        )

        alert.addAction(
            UIAlertAction(title: "Cancel", style: .cancel) { [weak self] _ in
                self?.pendingResult?(nil)
                self?.cleanup()
            })

        alert.addAction(
            UIAlertAction(title: "Settings", style: .default) { [weak self] _ in
                if let settingsUrl = URL(string: UIApplication.openSettingsURLString) {
                    UIApplication.shared.open(settingsUrl)
                }
                self?.pendingResult?(nil)
                self?.cleanup()
            })
        
        viewController.present(alert, animated: true)
    }

    // MARK: - Picker Presentation

    private func presentCamera() {
        DispatchQueue.main.async { [weak self] in
            guard let self = self else { return }
            guard let viewController = self.presentingViewController else {
                print("🎬 [iOS BytePlus] ERROR: No presenting view controller")
                self.pendingResult?(
                    FlutterError(
                        code: "NO_CONTROLLER",
                        message: "Unable to present camera",
                        details: nil
                    ))
                self.cleanup()
                return
            }
            
            let picker = UIImagePickerController()
            picker.delegate = self
            picker.sourceType = .camera
            picker.mediaTypes = [self.getVideoMediaType()]
            picker.videoQuality = self.videoQuality
            picker.videoMaximumDuration = self.maxVideoDuration
            picker.allowsEditing = false  // Set to true if you want basic trim
            picker.cameraCaptureMode = .video

            self.imagePicker = picker

            print("🎬 [iOS BytePlus] Presenting camera picker...")
            viewController.present(picker, animated: true) {
                print("🎬 [iOS BytePlus] Camera picker presented")
            }
        }
    }

    private func presentGallery() {
        DispatchQueue.main.async { [weak self] in
            guard let self = self else { return }
            guard let viewController = self.presentingViewController else {
                print("🎬 [iOS BytePlus] ERROR: No presenting view controller")
                self.pendingResult?(FlutterError(
                        code: "NO_CONTROLLER",
                        message: "Unable to present gallery",
                    details: nil
                ))
                self.cleanup()
                return
            }
            
            let picker = UIImagePickerController()
            picker.delegate = self
            picker.sourceType = .photoLibrary
            picker.mediaTypes = [self.getVideoMediaType()]
            picker.videoQuality = self.videoQuality
            picker.allowsEditing = false  // Set to true if you want basic trim

            self.imagePicker = picker

            print("🎬 [iOS BytePlus] Presenting gallery picker...")
            viewController.present(picker, animated: true) {
                print("🎬 [iOS BytePlus] Gallery picker presented")
            }
        }
    }

    // MARK: - Helper Methods

    private func getVideoMediaType() -> String {
        if #available(iOS 14.0, *) {
            return UTType.movie.identifier
        } else {
            return kUTTypeMovie as String
        }
    }

    private func cleanup() {
        self.imagePicker = nil
        self.pendingResult = nil
        self.presentingViewController = nil
    }
    
    // MARK: - Video Processing

    private func handleVideoSelection(url: URL) {
        print("🎬 [iOS BytePlus] Processing selected video: \(url.path)")

        // For now, return the path directly
        // In full BytePlus implementation, you would:
        // 1. Copy video to temporary directory if needed
        // 2. Open BytePlus editor with the video
        // 3. Wait for user to finish editing
        // 4. Return the edited video path

        let videoPath = url.path
        print("🎬 [iOS BytePlus] Returning video path to Flutter: \(videoPath)")
        
        // Check if video file exists
        if FileManager.default.fileExists(atPath: videoPath) {
            pendingResult?(videoPath)
        } else {
            print("🎬 [iOS BytePlus] ERROR: Video file doesn't exist at path: \(videoPath)")

            // Try to copy to temp directory
            do {
                let tempDir = FileManager.default.temporaryDirectory
                let fileName = url.lastPathComponent
                let tempURL = tempDir.appendingPathComponent(fileName)

                // Remove if exists
                if FileManager.default.fileExists(atPath: tempURL.path) {
                    try FileManager.default.removeItem(at: tempURL)
                }

                // Copy file
                try FileManager.default.copyItem(at: url, to: tempURL)
                print("🎬 [iOS BytePlus] Video copied to temp: \(tempURL.path)")
                pendingResult?(tempURL.path)
            } catch {
                print("🎬 [iOS BytePlus] ERROR copying video: \(error)")
                pendingResult?(
                    FlutterError(
                        code: "VIDEO_PROCESSING_ERROR",
                        message: "Failed to process video: \(error.localizedDescription)",
                    details: nil
                    ))
            }
        }

        cleanup()
    }
}

// MARK: - UIImagePickerControllerDelegate

extension BytePlusHelper: UIImagePickerControllerDelegate, UINavigationControllerDelegate {
    
    func imagePickerController(
        _ picker: UIImagePickerController,
        didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey: Any]
    ) {
        print("🎬 [iOS BytePlus] User selected media")
        
        picker.dismiss(animated: true) { [weak self] in
            guard let self = self else { return }

            // Try to get video URL
            var videoURL: URL?

            // Check in order of preference
            if let mediaURL = info[.mediaURL] as? URL {
                videoURL = mediaURL
                print("🎬 [iOS BytePlus] Got video from mediaURL: \(mediaURL.path)")
            } else if let imageURL = info[.imageURL] as? URL {
                videoURL = imageURL
                print("🎬 [iOS BytePlus] Got video from imageURL: \(imageURL.path)")
            } else if #available(iOS 11.0, *), let asset = info[.phAsset] as? PHAsset {
                print("🎬 [iOS BytePlus] Got PHAsset, exporting video...")
                self.exportVideo(from: asset)
                return
            }

            if let url = videoURL {
                self.handleVideoSelection(url: url)
            } else {
                print("🎬 [iOS BytePlus] ERROR: No video URL found in picker result")
                self.pendingResult?(
                    FlutterError(
                        code: "NO_VIDEO",
                        message: "No video found in selection",
                        details: nil
                    ))
                self.cleanup()
            }
        }
    }
    
    func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
        print("🎬 [iOS BytePlus] User cancelled picker")

        picker.dismiss(animated: true) { [weak self] in
            self?.pendingResult?(nil)  // Return null for cancellation
            self?.cleanup()
        }
    }

    // MARK: - PHAsset Export

    private func exportVideo(from asset: PHAsset) {
        let options = PHVideoRequestOptions()
        options.version = .current
        options.deliveryMode = .highQualityFormat
        options.isNetworkAccessAllowed = true

        PHImageManager.default().requestAVAsset(forVideo: asset, options: options) {
            [weak self] (avAsset, audioMix, info) in
            guard let self = self else { return }

            guard let urlAsset = avAsset as? AVURLAsset else {
                print("🎬 [iOS BytePlus] ERROR: Could not get AVURLAsset")
                DispatchQueue.main.async {
                    self.pendingResult?(
                        FlutterError(
                            code: "EXPORT_ERROR",
                            message: "Failed to export video",
                            details: nil
                        ))
                    self.cleanup()
                }
                return
            }

            let url = urlAsset.url
            print("🎬 [iOS BytePlus] Exported video URL: \(url.path)")

            DispatchQueue.main.async {
                self.handleVideoSelection(url: url)
            }
        }
    }
}





#### error messages:
Could not build the precompiled application for the device.
Swift Compiler Error (Xcode): Cannot find 'EOLicense' in scope
/Users/dimejii/dev/mobile_app_v2/ios/Runner/BytePlusHelper.swift:63:8

Swift Compiler Error (Xcode): Cannot find 'EOSDK' in scope
/Users/dimejii/dev/mobile_app_v2/ios/Runner/BytePlusHelper.swift:71:16

Swift Compiler Error (Xcode): Cannot find 'EOSDK' in scope
/Users/dimejii/dev/mobile_app_v2/ios/Runner/BytePlusHelper.swift:73:20

Swift Compiler Error (Xcode): Cannot find 'EOSDK' in scope
/Users/dimejii/dev/mobile_app_v2/ios/Runner/BytePlusHelper.swift:73:45

Swift Compiler Error (Xcode): Cannot find 'EOSDK' in scope
/Users/dimejii/dev/mobile_app_v2/ios/Runner/BytePlusHelper.swift:80:24

Swift Compiler Error (Xcode): Cannot find 'EOSDK' in scope
/Users/dimejii/dev/mobile_app_v2/ios/Runner/BytePlusHelper.swift:81:28

Swift Compiler Error (Xcode): Cannot find 'EOSDK' in scope
/Users/dimejii/dev/mobile_app_v2/ios/Runner/BytePlusHelper.swift:82:24

Swift Compiler Error (Xcode): Cannot find 'EOSDK' in scope
/Users/dimejii/dev/mobile_app_v2/ios/Runner/BytePlusHelper.swift:82:52

Swift Compiler Error (Xcode): Cannot find 'EOSDK' in scope
/Users/dimejii/dev/mobile_app_v2/ios/Runner/BytePlusHelper.swift:86:20

Swift Compiler Error (Xcode): Cannot infer type of closure parameter 'success' without a type annotation
/Users/dimejii/dev/mobile_app_v2/ios/Runner/BytePlusHelper.swift:63:68

Swift Compiler Error (Xcode): Cannot infer type of closure parameter 'errMsg' without a type annotation
/Users/dimejii/dev/mobile_app_v2/ios/Runner/BytePlusHelper.swift:63:77

Swift Compiler Error (Xcode): Value of type 'BytePlusHelper' has no member 'isAuthSucceeded'
/Users/dimejii/dev/mobile_app_v2/ios/Runner/BytePlusHelper.swift:68:22