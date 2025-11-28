The issues face are:
 i. one if you open the effectoneModule.swift file will see a commented long section, the if i used that block of code it runs but with the error on the screen when I try to use the byteplus editor
 "src="https://github.com/user-attachments/assets/8ff8b2ff-1c4e-4f1f-b39f-81c7156d4e88"/>
 ### the code is state below
 // import EOExportUI
// import Foundation
// import Toast
// import UIKit

// #if canImport(EffectOneKit)
//     import EffectOneKit
// #else
//     import EOEasyEditorUI
//     import EOEasyRecorderUI
//     import EOAuthCenter
// #endif

// class EffectOneModule: NSObject, EODraftBoxControllerDelegate {
//     var isAuthSucceeded: Bool = false
//     var parentVC: UIViewController

//     private weak var recorderVC: UIViewController?

//     init(parentVC: UIViewController) {
//         self.parentVC = parentVC
//     }
//     private func initEOSDK() {
//         EOSDK.initSDK { [weak self] in
//             self?.resourceConfig()
//         }
//     }

// //    public func makeAuth() {
// //        let config = EOAuthorizationConfig { initializer in
// //            initializer.isOnline = false
// //            initializer.isOversea = false
// //
// //            // Exact license filename
// //            let licenseFileName = "lykluk_test_20251027_20251231_com.lykluk.lyklukDev_1.8.0_472"
// //
// //            // Look inside Runner/License
// //            if let licensePath = Bundle.main.path(
// //                forResource: licenseFileName,
// //                ofType: "licbag",
// //                inDirectory: "License"
// //            ) {
// //                initializer.licensePathForOffline = licensePath
// //                print("✅ License path:", licensePath)
// //            } else {
// //                print("❌ License not found inside Runner/License/\(licenseFileName).licbag")
// //            }
// //
// //            // Token handling
// //            let token: String =
// //                UserDefaults.standard.value(forKey: "effectone.debug.token") as? String ?? ""
// //            if token.isEmpty {
// //                initializer.licenseTokenForOnline = "ncfSwHVQ_PVSULrTem-N6yDyKGEg3gySLPIWnE3iALc"
// //            } else {
// //                initializer.licenseTokenForOnline = token
// //            }
// //        }
// //
// //        EOAuthorization.sharedInstance().makeAuth(with: config) { isSuccess, errMsg in
// //            self.isAuthSucceeded = isSuccess
// //            if !isSuccess {
// //                print("❌ Auth failed:", errMsg)
// //            } else {
// //                print("✅ Auth succeeded")
// //                self.initEOSDK()
// //            }
// //        }
// //    }

//      public func makeAuth() {
//          let licenseFileName = "lykluk_test_20251027_20251231_com.lykluk.lyklukDev_1.8.0_472"
//          let config = EOAuthorizationConfig { [self] initializer in
//              //  The authentication method is offline authentication.
//              initializer.isOnline = false
//              //  Set the path where the offline certificate is located
//              // Look inside Runner/License
//              if let licensePath = Bundle.main.path(
//                  forResource: licenseFileName,
//                  ofType: "licbag",
//                  inDirectory: "License"
//              ) {
//                  initializer.licensePathForOffline = licensePath
//                  print("✅ License path:", licensePath)
//              } else {
//                  print("❌ License not found inside Runner/License/\(licenseFileName).licbag")
//              }
//          }

//          // Start authentication
//          EOAuthorization.sharedInstance().makeAuth(with: config) { isSuccess, errMsg in
//              self.isAuthSucceeded = isSuccess
//              if !self.isAuthSucceeded {
//                  //  Authentication failed to print error code
//                  print(errMsg)
//              } else {
//                  // Authentication successful, initialize EOSDK and resource allocation
//                  EOSDK.initSDK { [weak self] in
//                      EOSDK.setResourceBaseDir(
//                          EOSDK.defaultResourceDir(self!.localBundle().bundlePath))
//                      EOSDK.setResourceDefaultBuiltInConfig(
//                          EOSDK.defaultPanelConfigDir(self!.localBundle().bundlePath))
//                  }
//              }
//          }
//      }

//     func resourcePath() -> String {
//         // Use iOS app's Documents directory for downloaded resources
//         // NOT Bundle.main.resourcePath (which would require bundling resources)
//         let documentsPath = NSSearchPathForDirectoriesInDomains(
//             .documentDirectory, .userDomainMask, true)[0]
//         let resourcePath = "\(documentsPath)/BytePlusResources"

//         // Create directory if it doesn't exist
//         if !FileManager.default.fileExists(atPath: resourcePath) {
//             try? FileManager.default.createDirectory(
//                 atPath: resourcePath, withIntermediateDirectories: true)
//         }

//         print("🎬 [BytePlus] Resource path: \(resourcePath)")
//         return resourcePath
//     }

//     private func resourceConfig() {
//         let token: String =
//             UserDefaults.standard.value(forKey: "effectone.debug.token") as? String ?? ""
//         if token.isEmpty {
//             EOSDK.setResourceToken("ncfSwHVQ_PVSULrTem-N6yDyKGEg3gySLPIWnE3iALc")
//         } else {
//             EOSDK.setResourceToken(token)
//         }

//         let online =
//             UserDefaults.standard.value(forKey: "effectone.debug.resource.state") as? Int ?? 0
//         if online == 0 {  //  {zh} 内置资源  {en} built-in resources
//             EOSDK.setResourceBaseDir(EOSDK.defaultResourceDir(localBundle().bundlePath))
//             EOSDK.setResourceDefaultBuiltInConfig(
//                 EOSDK.defaultPanelConfigDir(localBundle().bundlePath))
//         } else if online == 1 {  //  {zh} 沙盒资源  {en} Sandbox Resources
//             let baseDir = "\(EOSDK.getEODocumentRootDir())/sandbox"
//             EOSDK.setResourceBaseDir(EOSDK.defaultResourceDir(baseDir))
//             EOSDK.setResourceDefaultBuiltInConfig(EOSDK.defaultPanelConfigDir(baseDir))
//         } else if online == 2 {  // BOE
//             let downloadBaseDir = EOSDK.getEODocumentRootDir() + "/resource"
//             EOSDK.setResourceBaseDir(EOSDK.defaultResourceDir(downloadBaseDir))
//             EOSDK.setRemoteAddrConfig(
//                 EORemoteAddrConfig(
//                     configDomain: "https://artob-boe.bytedance.net",
//                     configPath: "/api/ck/panels/tabs",
//                     resourceDomain: "https://artob-boe.bytedance.net",
//                     resourcePath: "/api/ck/ar_keys/contents/files/v3",
//                     modelDomain: "https://artob-boe.bytedance.net",
//                     modelPath: "/api/ck/model/files/v1")
//             )
//             EOSDK.useRemoteConfig(true, useRemoteResource: true)
//         } else if online == 3 {  // PPE
//             let downloadBaseDir = EOSDK.getEODocumentRootDir() + "/resource"
//             EOSDK.setResourceBaseDir(EOSDK.defaultResourceDir(downloadBaseDir))
//             let addrConfig = EORemoteAddrConfig(
//                 configDomain: "https://ar.volces.com",
//                 configPath: "/api/ck/panels/tabs",
//                 resourceDomain: "https://ar.volces.com",
//                 resourcePath: "/api/ck/ar_keys/contents/files/v3",
//                 modelDomain: "https://ar.volces.com",
//                 modelPath: "/api/ck/model/files/v1")
//             addrConfig.headers = [
//                 "x-tt-env": "ppe_douyin_01",
//                 "x-use-ppe": "1"
//             ]
//             EOSDK.setRemoteAddrConfig(addrConfig)
//             EOSDK.useRemoteConfig(true, useRemoteResource: true)
//         } else if online == 4 {  // PROD
//             let downloadBaseDir = EOSDK.getEODocumentRootDir() + "/resource"
//             EOSDK.setResourceBaseDir(EOSDK.defaultResourceDir(downloadBaseDir))
//             EOSDK.setRemoteAddrConfig(
//                 EORemoteAddrConfig(
//                     configDomain: "https://ar.volces.com",
//                     configPath: "/api/ck/panels/tabs",
//                     resourceDomain: "https://ar.volces.com",
//                     resourcePath: "/api/ck/ar_keys/contents/files/v3",
//                     modelDomain: "https://ar.volces.com",
//                     modelPath: "/api/ck/model/files/v1")
//             )
//             EOSDK.useRemoteConfig(true, useRemoteResource: true)
//         } else if online == 5 {  //  {zh} 自己部署的资源，按需下载  {en} Own deployed resources, download on demand
//             let downloadBaseDir = EOSDK.getEODocumentRootDir() + "/download_needed"
//             EOSDK.setResourceBaseDir(EOSDK.defaultResourceDir(downloadBaseDir))
//             let addrConfig = EORemoteAddrConfig(
//                 configDomain: "http://81.70.184.54",
//                 configPath: "/wallpaper/eo/config",
//                 resourceDomain: "http://81.70.184.54",
//                 resourcePath: "/wallpaper/eo/resource",
//                 modelDomain: "http://81.70.184.54",
//                 modelPath: "/wallpaper/eo/resource")
//             addrConfig.iconUrlPrefix = "http://81.70.184.54/icons/"
//             EOSDK.setRemoteAddrConfig(addrConfig)
//             EOSDK.useRemoteConfig(true, useRemoteResource: true)
//         }
//     }

//     public func showRecorderViewController() {
//         if !self.isAuthSucceeded {
//             self.showToast("authority is failed!,please check it.")
//             return
//         }

//         let config = EORecorderConfig { initializer in
//             // config recorder if needed

//             initializer.configRecorderViewController { recorderVCInitializer in
//                 // config recoderViewController if needed
//             }
//         }

//         EORecorderViewController.startRecorder(
//             with: config, presenter: self.parentVC, delegate: self
//         ) { [weak self] error in
//             if let err = error as? NSError, let presenter = self?.parentVC {
//                 self?.showErrorAlert(err, presenter: presenter)
//             }
//         }
//     }

//     public func showEditorViewController(info: EORecordInfo, presenter: UIViewController) {
//         if !self.isAuthSucceeded {
//             self.showToast("authority is failed!,please check it.")
//             return
//         }

//         let config = EOEditorConfig { initializer in
//             // config editor if needed
//             initializer.configVideoEditorViewController { editorVCInitializer in
//                 // config videoEditorViewController if needed
//             }
//         }

//         let sceneConfig = EOEditorSceneConfig()
//         sceneConfig.resources = info.mediaAssets
//         sceneConfig.backgroundMusic = info.backgroundMusic
//         sceneConfig.coverImage = info.coverImage
//         sceneConfig.previewContentMode = info.source == .camera ? .aspectFill : .aspectFit
//         sceneConfig.fromType = info.source == .camera ? "camera" : "album"

//         EOVideoEditorViewController.startEditor(
//             with: config, sceneConfig: sceneConfig, presenter: presenter, delegate: self
//         ) { [weak self, weak presenter] error in
//             if let err = error as? NSError, let vc = presenter {
//                 self?.showErrorAlert(err, presenter: vc)
//             }
//         }
//     }

//     public func showDraftViewController() {
//         if !self.isAuthSucceeded {
//             self.showToast("authority is failed!,please check it.")
//             return
//         }

//         EODraftBoxController.presentDraftVCDelegate(self)
//     }

//     private func showErrorAlert(_ err: NSError, presenter: UIViewController) {
//         let msg =
//             "\(NSLocalizedString("eo_home_error_alert_title", comment: ""))\(err.domain):\(err.code)"
//         let alert = UIAlertController(title: nil, message: msg, preferredStyle: .alert)
//         alert.addAction(
//             UIAlertAction(
//                 title: NSLocalizedString("eo_home_error_alert_ok", comment: ""), style: .cancel))
//         presenter.present(alert, animated: true)
//     }

//     public func showToast(_ text: String) {
//         guard let window = UIApplication.shared.keyWindow else {
//             return
//         }
//         window.makeToast(text, duration: 1.5, position: CSToastPositionCenter)
//     }

//     private func topMostViewController() -> UIViewController? {
//         var topViewController: UIViewController?
//         let window = UIApplication.shared.keyWindow
//         let rootViewController = window?.rootViewController
//         if let tabBar = rootViewController as? UITabBarController {
//             topViewController = tabBar.selectedViewController
//         } else {
//             topViewController = rootViewController
//         }
//         if let nav = topViewController as? UINavigationController {
//             topViewController = nav.topViewController
//         }
//         while topViewController?.presentedViewController != nil {
//             topViewController = topViewController?.presentedViewController
//             if let nav = topViewController as? UINavigationController {
//                 topViewController = nav.topViewController
//             }
//         }
//         return topViewController
//     }

//     func localBundle() -> Bundle {
//         Bundle(path: Bundle.main.path(forResource: "EOLocalResources", ofType: "bundle") ?? "")
//             ?? Bundle.main
//     }

//     func draftBoxController(_ controller: Any!, didFinishWithDraft draft: Any!) {
//         print("🎬 [BytePlus] Draft selected")
//         if let viewController = controller as? UIViewController {
//             viewController.dismiss(animated: true)
//         }
//     }

//     func draftBoxControllerDidCancel(_ controller: Any!) {
//         print("🎬 [BytePlus] Draft cancelled")
//         if let viewController = controller as? UIViewController {
//             viewController.dismiss(animated: true)
//         }
//     }

// }

// extension EffectOneModule: EORecorderViewControllerDelegate {
//     func recorderViewControllerDidTapAlbum(_ recorderViewController: EORecorderViewController) {
//         self.recorderVC = recorderViewController

//         guard let resourcePicker = EOInjectContainer.shared().resourcePickerSharedImpl else {
//             return
//         }

//         resourcePicker.pickResourcesFromRecorder { [weak self] resources, error, cancel in
//             guard !resources.isEmpty else {
//                 return
//             }

//             let info = EORecordInfo()
//             info.mediaAssets = resources
//             info.source = .album

//             if let presenter = self?.topMostViewController() {
//                 self?.showEditorViewController(info: info, presenter: presenter)
//             }
//         }
//     }

//     func recorderViewController(
//         _ recorderViewController: EORecorderViewController,
//         didFinishRecordingMediaWith info: EORecordInfo
//     ) {
//         self.recorderVC = recorderViewController

//         self.showEditorViewController(info: info, presenter: recorderViewController)
//     }
// }

// extension EffectOneModule: EOVideoEditorViewControllerDelegate {
//     func videoEditorViewControllerDidCancel(
//         _ videoEditorViewController: EOVideoEditorViewController
//     ) {
//         if let recorder = recorderVC {
//             recorder.dismiss(animated: true, completion: nil)
//         } else {
//             videoEditorViewController.dismiss(animated: true, completion: nil)
//         }
//     }

//     func videoEditorViewControllerTapNext(
//         _ exportModel: EOExportModel, presentVC viewController: UIViewController
//     ) {
//         EOExportViewController.startExport(with: exportModel, presentVC: viewController)
//     }
// }

///////////////////////////////////////////////////////////////////////////////////////////
## then if used the code given it fail  to run with this error image is below
<img width="748" height="259" alt="Screenshot 2025-11-28 at 2 34 59 PM" src="https://github.com/user-attachments/assets/40a0cbb6-f31c-4975-9eea-04d09714a8e3" />

## code is below
import Foundation
import UIKit
import Toast
// {zh} 需要引入EffectOneKit {en} Need to introduce EffectOneKit
import EffectOneKit
// {zh} 需要引入导出组件 {en} Export component needs to be introduced
import EOExportUI
// {zh} 需要引入剪同款组件 {en} You need to introduce and cut the same components.
//import CutSameUIIF

class EffectOneModule: NSObject {
    let authFileName: String = "lykluk_test_20251027_20251130_com.lykluk.lyklukDev_1.8.0_462.licbag"
    // {zh} 存储鉴权状态 {en} store authentication state
    var isAuthSucceeded: Bool = false
    // {zh} 存储被使用的VC，用于弹出界面 {en} Store the used VC for the pop-up interface
    var parentVC: UIViewController

    // {zh} 暂存拍摄组件的应用，用于退出编辑组件时，隐藏相册选图页面 {en} The application that temporarily stores the shooting component is used to hide the album selection page when exiting the editing component
    private weak var recorderVC: UIViewController?

    init(parentVC: UIViewController) {
        self.parentVC = parentVC
    }
    // {zh} 启动鉴权 {en} enable authentication
    public func makeAuth() {
        let config = EOAuthorizationConfig { [self] initializer in
            // {zh} 鉴权方式是离线鉴权 {en} The authentication method is offline authentication.
            initializer.isOnline = false
            // {zh} 设置离线证书所在的路径 {en} Set the path where the offline certificate is located
            initializer.licensePathForOffline = self.localBundle().path(
                forResource: self.authFileName, ofType: nil, inDirectory: "License")!
        }
        // {zh} 开始鉴权 {en} Start authentication
        EOAuthorization.sharedInstance().makeAuth(with: config) { isSuccess, errMsg in
            self.isAuthSucceeded = isSuccess
            if !self.isAuthSucceeded {
                // {zh} 鉴权失败打印错误码 {en} Authentication failed to print error code
                print(errMsg)
            } else {
                // {zh} 鉴权成功，初始化EOSDK及资源配置 {en} Authentication successful, initialize EOSDK and resource allocation
                EOSDK.initSDK { [weak self] in
                    EOSDK.setResourceBaseDir(
                        EOSDK.defaultResourceDir(self!.localBundle().bundlePath))
                    EOSDK.setResourceDefaultBuiltInConfig(
                        EOSDK.defaultPanelConfigDir(self!.localBundle().bundlePath))
                }
            }
        }
    }
    //  {zh} 进入基础编辑组件的源  {en} Access to the source of the basic editing component
    enum EditorParams {
        case normal(EORecordInfo)
        case draft(EOSDKDraftModel)
    }

    // {zh} 获取资源根目录 {en} Get resource root directory
    private func localBundle() -> Bundle {
        Bundle(path: Bundle.main.path(forResource: "EOLocalResources", ofType: "bundle") ?? "")
            ?? Bundle.main
    }

    private func showErrorAlert(_ err: NSError, presenter: UIViewController) {
        let msg =
            "\(NSLocalizedString("eo_home_error_alert_title", comment: ""))\(err.domain):\(err.code)"
        let alert = UIAlertController(title: nil, message: msg, preferredStyle: .alert)
        alert.addAction(
            UIAlertAction(
                title: NSLocalizedString("eo_home_error_alert_ok", comment: ""), style: .cancel))
        presenter.present(alert, animated: true)
    }

    public func showToast(_ text: String) {
        guard let window = UIApplication.shared.keyWindow else {
            return
        }
        window.makeToast(text, duration: 1.5, position: CSToastPositionCenter)
    }

    private func topMostViewController() -> UIViewController? {
        var topViewController: UIViewController?
        let window = UIApplication.shared.keyWindow
        let rootViewController = window?.rootViewController
        if let tabBar = rootViewController as? UITabBarController {
            topViewController = tabBar.selectedViewController
        } else {
            topViewController = rootViewController;
        }
        if let nav = topViewController as? UINavigationController {
            topViewController = nav.topViewController
        }
        while topViewController?.presentedViewController != nil {
            topViewController = topViewController?.presentedViewController
            if let nav = topViewController as? UINavigationController {
                topViewController = nav.topViewController
            }
        }
        return topViewController
    }

    func showAlert(
        withTitle title: String,
        message: String,
        cancelTitle: String?,
        confirmTitle: String?,
        cancelHandler: (() -> Void)?,
        confirmHandler: (() -> Void)?
    ) {
        let alert = UIAlertController(title: title, message: message, preferredStyle: .alert)

        if let cancelTitle = cancelTitle {
            let cancelAction = UIAlertAction(title: cancelTitle, style: .cancel) { _ in
                cancelHandler?()
            }
            alert.addAction(cancelAction)
        }

        if let confirmTitle = confirmTitle {
            let confirmAction = UIAlertAction(title: confirmTitle, style: .default) { _ in
                confirmHandler?()
            }
            alert.addAction(confirmAction)
        }

        self.parentVC.present(alert, animated: true, completion: nil)
    }
}

extension EffectOneModule {
    // {zh} 启动基础编辑组件 {en} Start the basic editing component
    public func showRecorderViewController() {
        if !self.isAuthSucceeded {
            self.showToast("authority is failed!,please check it.")
            return
        }
        // {zh} 构造拍摄组件默认配置 {en} Construct the default configuration of the shooting component
        let config = EORecorderConfig { initializer in
            // config recorder if needed

            initializer.configRecorderViewController { recorderVCInitializer in
                // config recoderViewController if needed
            }
        }
        // {zh} 显示拍摄页面 {en} Show the shooting page
        EORecorderViewController.startRecorder(
            with: config, presenter: self.parentVC, delegate: self
        ) { [weak self] error in
            if let err = error as? NSError, let presenter = self?.parentVC {
                self?.showErrorAlert(err, presenter: presenter)
            }
        }
    }
    // {zh} 视频合拍 {en} Video co-production
    public func showDuetViewController() {
        if !self.isAuthSucceeded {
            self.showToast("authority is failed!,please check it.")
            return
        }
        // {zh} 检测相册权限 {en} Detect album permissions
        EOAlbumHelper.eoCheckAlbumAuthorized { [weak self] in
            // {zh} 使用EOSDK中相册组件来实现选图功能 {en} Use the album component in EOSDK to realize the picture selection function
            guard let resourcePicker = EOInjectContainer.shared().resourcePickerSharedImpl else {
                return
            }
            // {zh} 选择相册素材结束 {en} Select album material to end
            resourcePicker.pickVideoForDuet(completion: { [weak self] resources, error, cancel in
                if let resorce = resources.first {
                    // {zh} 进入合拍页面 {en} Enter the match page
                    self?.gotoDuetViewController(withVideoURL: resorce.url!)
                }

            })

        } restrictedOrDenied: { [weak self] in
            self?.showAlert(
                withTitle: NSLocalizedString(
                    "eo_home_camera_album_unauth_alert_title", comment: ""),
                message: NSLocalizedString(
                    "eo_home_camera_album_unauth_alert_message", comment: ""),
                cancelTitle: NSLocalizedString("eo_home_camera_unauth_alert_cancel", comment: ""),
                confirmTitle: NSLocalizedString("eo_home_camera_unauth_alert_confirm", comment: ""),
                cancelHandler: nil,
                confirmHandler: {
                    if #available(iOS 10.0, *) {
                        UIApplication.shared.open(
                            URL(string: UIApplication.openSettingsURLString)!, options: [:]
                        ) { success in
                            // Completion handler
                        }
                    } else {
                        UIApplication.shared.openURL(
                            URL(string: UIApplication.openSettingsURLString)!)
                    }
                })
        }

    }
    // {zh} 进入合拍页面 {en} Enter the match page
    public func gotoDuetViewController(withVideoURL videoURL: URL) {
        if !self.isAuthSucceeded {
            self.showToast("authority is failed!,please check it.")
            return
        }

        let config = EORecorderConfig { initializer in
            // config recorder if needed
            initializer.configRecorderViewController { recorderVCInitializer in
                // config recoderViewController if needed
                recorderVCInitializer.musicBarHidden = true
                let sideConfig = EORecorderSideBarConfig()
                sideConfig.itemKeys = [
                    EORecordSideBarItemKey.barItemRotateCameraKey,
                    EORecordSideBarItemKey.barItemFlashKey,
                    EORecordSideBarItemKey.barItemMicKey,
                    EORecordSideBarItemKey.barItemDuetLayoutKey,
                    EORecordSideBarItemKey.barItemTimerKey,
                    EORecordSideBarItemKey.barItemFiltersKey,
                    EORecordSideBarItemKey.barItemBeautyKey,
                    EORecordSideBarItemKey.barItemSpeedKey,
                ]
                recorderVCInitializer.sideBarConfig = sideConfig
            }
        }

        EODuetViewController.startDuet(
            with: config, presenter: self.parentVC, duetVideoURL: videoURL, delegate: self,
            completion: { [weak self] error in
                if let err = error as? NSError, let presenter = self?.parentVC {
                    self?.showErrorAlert(err, presenter: presenter)
                }
            })
    }

    // {zh} 进入剪同款页面 {en} Enter CutSame page
    func showCutSameController() {
        //        CutSameRouter.toCutSameCategoryVC()
    }
}

extension EffectOneModule: EORecorderViewControllerDelegate {
    // {zh} 显示相册页面 {en} Show album page
    func recorderViewControllerDidTapAlbum(_ recorderViewController: EORecorderViewController) {
        // {zh} 暂存拍摄组件的应用，用于退出编辑组件时，隐藏相册选图页面 {en} The application that temporarily stores the shooting component is used to hide the album selection page when exiting the editing component
        self.recorderVC = recorderViewController

        // {zh} 使用EOSDK中相册组件来实现选图功能 {en} Use the album component in EOSDK to realize the picture selection function
        guard let resourcePicker = EOInjectContainer.shared().resourcePickerSharedImpl else {
            return
        }

        recorderViewController.pausePreview()

        resourcePicker.pickResourcesFromRecorder { [weak self] resources, error, cancel in
            guard !resources.isEmpty else {
                return;
            }

            let info = EORecordInfo()
            info.mediaAssets = resources
            info.source = .album
            // {zh} 完成选图后显示编辑页面 {en} Show the edit page after completing the selection
            if let presenter = self?.topMostViewController() {
                self?.showEditorViewController(.normal(info), presenter: presenter)
            }
        }
    }

    // {zh} 完成拍摄后，跳转进入编辑页面 {en} After finishing shooting, jump to the editing page
    func recorderViewController(
        _ recorderViewController: EORecorderViewController,
        didFinishRecordingMediaWith info: EORecordInfo
    ) {
        // {zh} 暂存拍摄组件的应用，用于退出编辑组件时，隐藏相册选图页面 {en} The application that temporarily stores the shooting component is used to hide the album selection page when exiting the editing component
        self.recorderVC = recorderViewController

        recorderViewController.pausePreview()
        // {zh} 进入基础编辑 {en} Enter basic editing
        self.showEditorViewController(.normal(info), presenter: recorderViewController)
    }
}

extension EffectOneModule {
    // {zh} 拍摄完成、从相册或者草稿进入基础编辑 {en} The shooting is completed or enter the basic editing from the album.
    func showEditorViewController(_ params: EditorParams, presenter: UIViewController) {
        if !self.isAuthSucceeded {
            self.showToast("authority is failed!,please check it.")
            return
        }
        // {zh} 构造编辑组件默认配置 {en} Construct editing component default configuration
        let config = EOEditorConfig { initializer in }
        // {zh} 设置编辑组件需要的输入参数 {en} Set the input parameters required by the editing component
        let sceneConfig = EOEditorSceneConfig()
        switch params {
        case .normal(let info):  // {zh} 从拍摄或相册进入基础编辑 {en} Go from shooting or album to basic editing
            sceneConfig.resources = info.mediaAssets
            sceneConfig.backgroundMusic = info.backgroundMusic
            sceneConfig.coverImage = info.coverImage
            sceneConfig.previewContentMode = info.source == .camera ? .aspectFill : .aspectFit
            sceneConfig.fromType = info.source == .camera ? "camera" : "album"
            // {zh} 判断是否合拍 {en} Determine whether it is in tune
            if info.isDuet == true {
                sceneConfig.editorPreviewForType = .duetEditor
                sceneConfig.duetMusic = info.duetUrl
            }
        case .draft(let model):  // {zh} 从草稿进入基础编辑 {en} From draft to basic editing
            sceneConfig.draftModel = model
        }
        // {zh} 显示编辑页面 {en} Show edit page
        EOVideoEditorViewController.startEditor(
            with: config, sceneConfig: sceneConfig, presenter: presenter, delegate: self
        ) { [weak self, weak presenter] error in
            if let err = error as? NSError, let vc = presenter {
                self?.showErrorAlert(err, presenter: vc)
            }
        };
    }
    // {zh} 启动草稿组件方法 {en} Start draft component method
    public func showDraftViewController() {
        if !self.isAuthSucceeded {
            self.showToast("authority is failed!,please check it.")
            return
        }
        // {zh} 显示草稿组件 {en} Show draft component 
        EODraftBoxController.presentDraftVCDelegate(self)
    }
    // {zh} 调用高光成片 {en} Call highlight film
    public func showHighLightViewController() {
        if !self.isAuthSucceeded {
            self.showToast("authority is failed!,please check it.")
            return
        }
        _showHighLightViewController()
    }
}
// MARK: highLightFirm extension EffectOneModule {
    public func _showHighLightViewController() {
        if !self.isAuthSucceeded {
            self.showToast("authority is failed!,please check it.")
            return
        }
        EOAlbumHelper.eoCheckAlbumAuthorized {  // {zh} 相册权限已打开 {en} Album permissions are turned on
            // {zh} 启动高光成片 {en} Start highlight film
            //            EOHighLightManager.toHighLight()
        } restrictedOrDenied: { [weak self] in
            self?.showAlert(
                withTitle: NSLocalizedString(
                    "eo_home_camera_album_unauth_alert_title", comment: ""),
                message: NSLocalizedString(
                    "eo_home_camera_album_unauth_alert_message", comment: ""),
                cancelTitle: NSLocalizedString("eo_home_camera_unauth_alert_cancel", comment: ""),
                confirmTitle: NSLocalizedString("eo_home_camera_unauth_alert_confirm", comment: ""),
                cancelHandler: nil,
                confirmHandler: {
                    if #available(iOS 10.0, *) {
                        UIApplication.shared.open(
                            URL(string: UIApplication.openSettingsURLString)!, options: [:]
                        ) { success in
                            // Completion handler
                        }
                    } else {
                        UIApplication.shared.openURL(
                            URL(string: UIApplication.openSettingsURLString)!)
                    }
                })
        }

    }
}

extension EffectOneModule: EOVideoEditorViewControllerDelegate {
    func videoEditorViewControllerDidCancel(
        _ videoEditorViewController: EOVideoEditorViewController
    ) {
        if let recorder = recorderVC {
            // {zh} 隐藏相册选图页面 {en} Hide album selection page
            recorder.dismiss(animated: true, completion: nil)
        } else {
            // {zh} 当从草稿进入编辑页面再退出编辑时，隐藏编辑页面 {en} Hide the edit page when entering the edit page from the draft and then exiting the edit
            videoEditorViewController.dismiss(animated: true, completion: nil)
        }
    }

    // {zh} 点击下一步按钮，启动导出页面 {en} Click the Next button to launch the export page
    func videoEditorViewControllerTapNext(
        _ exportModel: EOExportModel, presentVC viewController: UIViewController
    ) {
        let viewa = UIView.init(
            frame: CGRectMake(
                0, 0, viewController.view.frame.width, viewController.view.frame.height))
        let loading = EOLargeCircleLoadingView.init(
            frame: CGRectMake(
                viewController.view.frame.width / 2 - 25, viewController.view.frame.height / 2 - 25,
                50, 50))
        viewController.view.addSubview(viewa)
        viewa.addSubview(loading)
        loading.startAnimating()
        let manager = EOExportManager.init(model: exportModel)
        manager.exportVideo { [weak self] code, progress, path in
            guard let self = self else {
                return
            }
            if code == "exportProcess" {
                let progressString = String(format: "%.2f", progress!)
                FlutterMessageManager.shared.invokeMethod(
                    method: "exportProcess", message: progressString)
            } else if code == "exportError" {
                loading.stopAnimating()
                self.parentVC.dismiss(animated: true)
                FlutterMessageManager.shared.invokeMethod(
                    method: "exportError", message: "export error")
            } else if code == "exportDone" {
                loading.stopAnimating()
                self.parentVC.dismiss(animated: true)
                if let path = path?.path {
                    FlutterMessageManager.shared.invokeMethod(method: "exportDone", message: path)
                }
            }
        }
    }
}

extension EffectOneModule: EODraftBoxControllerDelegate {
    // {zh} 从草稿进入基础编辑组件 {en} Moving from draft to basic editing components
    func draftBoxController(
        _ draftBoxController: EODraftBoxController, didSelectDraft draftModel: EOSDKDraftModel
    ) {
        self.showEditorViewController(.draft(draftModel), presenter: draftBoxController)
    }
}

