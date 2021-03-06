# Comment Add Screenlet for Android [](id=comment-add-screenlet-for-android)

## Requirements [](id=requirements)

- Android SDK 4.1 (API Level 16) or above
- Liferay Portal 6.2 CE/EE, Liferay CE Portal 7.0/7.1, Liferay DXP
- Liferay Screens Compatibility app
  ([CE](http://www.liferay.com/marketplace/-/mp/application/54365664) or 
  [EE/DXP](http://www.liferay.com/marketplace/-/mp/application/54369726)). 
  This app is preinstalled in Liferay CE Portal 7.0/7.1 and Liferay DXP. 

## Compatibility [](id=compatibility)

- Android SDK 4.1 (API Level 16) or above

## Xamarin Requirements [](id=xamarin-requirements)

- Visual Studio 7.2
- Mono .NET framework 5.4.1.6

## Features [](id=features)

Comment Add Screenlet can add a comment to an asset in a Liferay instance. 

## JSON Services Used [](id=json-services-used)

Screenlets in Liferay Screens call JSON web services in the portal. This 
Screenlet calls the following services and methods.

| Service | Method | Notes |
| ------- | ------ | ----- |
| `ScreenscommentService` (Screens compatibility plugin) | `addComment` |  |

## Module [](id=module)

- None

## Views [](id=views)

- Default

The Default View uses Android's `EditText` and `Button` elements to show an add 
comment dialog. Other Views may use different components to show this dialog. 

![Figure 1: Comment Add Screenlet using the Default View.](../../../images/screens-android-commentadd.png)

## Offline [](id=offline)

This Screenlet supports offline mode so it can function without a network 
connection. For more information on how offline mode works, see the 
[tutorial on its architecture](/develop/tutorials/-/knowledge_base/7-1/architecture-of-offline-mode-in-liferay-screens). 
Here are the offline mode policies that you can use with this Screenlet: 

| Policy | What happens | When to use |
|--------|--------------|-------------|
| `REMOTE_ONLY` | The Screenlet sends the data to the Liferay instance. If a connection issue occurs, the Screenlet uses the listener to notify the developer about the error. If the Screenlet successfully sends the data, it also stores it in the local cache. | Use this policy when you always need to send updated data, and send nothing when there's no connection. |
| `CACHE_ONLY` | The Screenlet sends the data to the local cache. If an error occurs, the Screenlet uses the listener to notify the developer. | Use this policy when you always need to store local data without sending remote information under any circumstance. |
| `REMOTE_FIRST` | The Screenlet sends the data to the Liferay instance. If this succeeds, the Screenlet also stores the data in the local cache. If a connection issue occurs, the Screenlet stores the data to the local cache and sends it to the Liferay instance when the connection is restored. If an error occurs, the Screenlet uses the listener to notify the developer. | Use this policy to send the most recent version of the data when connected, and store the data for later synchronization when there's no connection. |
| `CACHE_FIRST` | The Screenlet sends the data to the local cache, then sends it to the Liferay instance. If sending the data to the Liferay instance fails, the Screenlet still stores the data locally and then notifies the developer about any errors that occur (including connectivity errors). | Use this policy to save bandwidth and store local (but possibly outdated) data. |

## Required Attributes [](id=required-attributes)

- `className`
- `classPK`

## Attributes [](id=attributes)

| Attribute | Data type | Explanation |
|-----------|-----------|-------------|
| `layoutId` | `@layout` | The layout to use to show the View.|
| `className` | `string` | The asset's fully qualified class name. For example, a blog entry's `className` is `com.liferay.blogs.model.BlogsEntry`. The `className` and `classPK` attributes are required to instantiate the Screenlet. | 
| `classPK` | `number` | The asset's unique identifier. The `className` and `classPK` attributes are required to instantiate the Screenlet. |
| `cachePolicy` | `string` | The offline mode setting. See [the Offline section](/develop/reference/-/knowledge_base/7-1/comment-add-screenlet-for-android#offline) for details. |

## Listener [](id=listener)

Comment Add Screenlet delegates some events to a class that implements 
`CommentAddListener`. This interface lets you implement the following methods: 

- `onAddCommentSuccess(CommentEntry commentEntry)`: Called when the Screenlet 
  successfully adds a comment to the asset. 

- `error(Exception e, String userAction)`: Called when an error occurs in the 
  process. The `userAction` argument distinguishes the specific action in which 
  the error occurred. 
