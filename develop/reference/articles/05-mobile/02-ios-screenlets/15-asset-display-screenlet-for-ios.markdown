# Asset Display Screenlet for iOS [](id=asset-display-screenlet-for-ios)

## Requirements [](id=requirements)

- Xcode 9.3 or above
- iOS 11 SDK
- Liferay Portal 6.2 CE/EE, Liferay CE Portal 7.0/7.1, Liferay DXP
- Liferay Screens Compatibility app
  ([CE](http://www.liferay.com/marketplace/-/mp/application/54365664) or 
  [EE/DXP](http://www.liferay.com/marketplace/-/mp/application/54369726)). 
  This app is preinstalled in Liferay CE Portal 7.0/7.1 and Liferay DXP. 

## Compatibility [](id=compatibility)

- iOS 9 and above

## Xamarin Requirements [](id=xamarin-requirements)

- Visual Studio 7.2
- Mono .NET framework 5.4.1.6

## Features [](id=features)

Asset Display Screenlet can display an asset from a Liferay instance. The 
Screenlet can currently display Documents and Media files (`DLFileEntry` images, 
videos, audio files, and PDFs), blogs entries (`BlogsEntry`) and web content 
articles (`WebContent`). 

Asset Display Screenlet can also display your custom asset types. See 
[the delegate section of this document](/develop/reference/-/knowledge_base/7-1/asset-display-screenlet-for-ios#delegate) 
for details. 

## JSON Services Used [](id=json-services-used)

Screenlets in Liferay Screens call JSON web services in the portal. This 
Screenlet calls the following services and methods.

| Service | Method | Notes |
| ------- | ------ | ----- |
| `ScreensassetentryService` (Screens compatibility plugin) | `getAssetEntry` | With `entryId` |
| `ScreensassetentryService` (Screens compatibility plugin) | `getAssetEntry` | With `classPK` and `className` |
| `ScreensassetentryService` (Screens compatibility plugin) | `getAssetEntries` | With `entryQuery` |
| `ScreensassetentryService` (Screens compatibility plugin) | `getAssetEntries` | With `companyId`, `groupId`, and `portletItemName` |

## Module [](id=module)

- None

## Themes [](id=themes)

- Default

The Default Theme uses different UI elements to show each asset type. For 
example, it displays images with `UIImageView`, and blogs with `UILabel`. 

This Screenlet can also render other Screenlets: 

- Images: Image Display Screenlet
- Videos: Video Display Screenlet
- Audio: Audio Display Screenlet
- PDFs: PDF Display Screenlet
- Blog entries: Blogs Entry Display Screenlet
- Web content: Web Content Display Screenlet

These Screenlets can also be used alone without Asset Display Screenlet. 

![Figure 1: Asset Display Screenlet using the Default Theme.](../../../images/screens-ios-assetdisplay.png)

## Offline [](id=offline)

This Screenlet supports offline mode so it can function without a network 
connection. For more information on how offline mode works, see the 
[tutorial on its architecture](/develop/tutorials/-/knowledge_base/7-1/architecture-of-offline-mode-in-liferay-screens). 
Here are the offline mode policies that you can use with this Screenlet: 

| Policy | What happens | When to use |
|--------|--------------|-------------|
| `remote-only` | The Screenlet loads the data from the Liferay instance. If a connection issue occurs, the Screenlet uses the listener to notify the developer about the error. If the Screenlet successfully loads the data, it stores it in the local cache for later use. | Use this policy when you always need to show updated data, and show nothing when there's no connection. |
| `cache-only` | The Screenlet loads the data from the local cache. If the data isn't there, the Screenlet uses the listener to notify the developer about the error. | Use this policy when you always need to show local data, without retrieving remote information under any circumstance. |
| `remote-first` | The Screenlet loads the data from the Liferay instance. If this succeeds, the Screenlet shows the data to the user and stores it in the local cache for later use. If a connection issue occurs, the Screenlet retrieves the data from the local cache. If the data doesn't exist there, the Screenlet uses the listener to notify the developer about the error. | Use this policy to show the most recent version of the data when connected, but show an outdated version when there's no connection. |
| `cache-first` | The Screenlet loads the data from the local cache. If the data isn't there, the Screenlet requests it from the Liferay instance and notifies the developer about any errors that occur (including connectivity errors). | Use this policy to save bandwidth and loading time in case you have local (but probably outdated) data. |

## Required Attributes [](id=required-attributes)

- `assetEntryId`

Instead of `assetEntryId`, you can use both of these attributes: 

- `className`
- `classPK`

If you don't use the above attributes, you must use this attribute: 

- `portletItemName`

## Attributes [](id=attributes)

| Attribute | Data type | Explanation |
|-----------|-----------|-------------|
| `assetEntryId` | `number` | The primary key of the asset. | 
| `className` | `string` | The asset's fully qualified class name. For example, a blog entry's `className` is `com.liferay.blogs.model.BlogsEntry`. The `className` and `classPK` attributes are required to instantiate the Screenlet. | 
| `classPK` | `number` | The asset's unique identifier. The `className` and `classPK` attributes are required to instantiate the Screenlet. |
| `portletItemName` | `string` | The name of the [configuration template](/discover/portal/-/knowledge_base/7-1/configuration-templates) you used in the Asset Publisher. To use this feature, add an Asset Publisher to one of your site's pages (it may be a hidden page), configure the Asset Publisher's filter (in *Configuration* &rarr; *Setup* &rarr; *Asset Selection*), and then use the Asset Publisher's *Configuration Templates* option to save this configuration with a name. Use this name as this attribute's value. If there is more than one asset in the configuration, the Screenlet displays only the first one. | 
| `assetEntry` | `Asset` | The `Asset` object to display, selected from a list of assets. Note that if you use this attribute, the Screenlet doesn't need to call the server. |
| `autoLoad` | `boolean` | Whether the asset automatically loads when the Screenlet appears in the app's UI. The default value is `true`. |
| `offlinePolicy` | `string` | The offline mode setting. The default value is `remote-first`. See [the Offline section](/develop/reference/-/knowledge_base/7-1/asset-display-screenlet-for-ios#offline) for details. |

## Delegate [](id=delegate)

Asset Display Screenlet delegates some events to an object that conforms to 
the `AssetDisplayScreenletDelegate` protocol. This protocol lets you implement 
the following methods: 

- `- screenlet:onAssetResponse:`: Called when the Screenlet receives the asset. 

- `- screenlet:onAssetError:`: Called when an error occurs in the process. The 
  `NSError` object describes the error. 
   
- `- screenlet:onConfigureScreenlet:`: Called when the child Screenlet (the 
  Screenlet rendered inside Asset Display Screenlet) has been successfully 
  initialized. Use this method to configure or customize it. The example 
  implementation here sets the child Blogs Entry Display Screenlet's background 
  color to gray: 

        func screenlet(screenlet: AssetDisplayScreenlet, onConfigureScreenlet, 
            childScreenlet: BaseScreenlet?, onAsset asset: Asset) {
                if childScreenlet is BlogsEntryDisplayScreenlet {
                    childScreenlet?.screenletView?.backgroundColor = UIColor.grayColor()
                }
        }

- `- screenlet:onAsset:`: Called to render a custom asset. The following example 
  implementation renders a portal user (`User`). If the asset is a user, this 
  method instantiates a custom `UserProfileView` to render that user: 

        public func screenlet(screenlet: AssetDisplayScreenlet, onAsset asset: Asset) -> UIView? {
            if let type = asset.attributes["object"]?.allKeys.first as? String {
                if type == "user" {
                    let view = NSBundle.mainBundle().loadNibNamed("UserProfileView", owner: self, 
                        options: nil)![0] as? UserProfileView

                    view?.user = User(attributes: asset.attributes)

                    return view
                }
            }
            return nil
        }
