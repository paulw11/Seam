![seamlogo](https://cloud.githubusercontent.com/assets/3306263/11775866/4b925354-a264-11e5-9bf6-bfdcf2cf9675.png)


[![Pod Version](https://img.shields.io/badge/pod-v0.6-blue.svg)](https://img.shields.io/cocoapods/v/Alamofire.svg)
[![Cocoapods Compatible](https://img.shields.io/badge/Cocoapods-Compatible-brightgreen.svg)](https://img.shields.io/badge/Cocoapods-Compatible-green.svg)
[![Carthage Compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)
[![Platform](https://img.shields.io/badge/platform-iOS%20--%20OSX-lightgrey.svg)](https://img.shields.io/badge/platform-iOS%20--%20OSX-lightgrey.svg)
[![License](https://img.shields.io/packagist/l/doctrine/orm.svg)](https://img.shields.io/packagist/l/doctrine/orm.svg)

Seam allows you to sync your CoreData Stores with CloudKit.

## Features
- Automatic mapping of CoreData Models to CloudKit Private Databases
- Background Sync 
- Conflict Resolution Policies
- Tutorials

## Requirements

- iOS 8.0+ / Mac OS X 10.10+
- Xcode 7.1+

## Installation

### Cocoapods

[CocoaPods](http://cocoapods.org) is a dependency manager for Cocoa projects. You can install it with the following command:

```bash
$ gem install cocoapods
```

To integrate Seam into your Xcode project using CocoaPods, specify it in your `Podfile`:

```ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '8.3'
use_frameworks!

pod 'Seam', '~> 0.6'
```

Then, run the following command:

```bash
$ pod install
```

### Carthage

[Carthage](https://github.com/Carthage/Carthage) is a decentralized dependency manager that builds your dependencies and provides you with binary frameworks.

You can install Carthage with [Homebrew](http://brew.sh/) using the following command:

```bash
$ brew update
$ brew install carthage
```

To integrate Seam into your Xcode project using Carthage, specify it in your `Cartfile`:

```ogdl
github "Seam/Seam" ~> 0.6
```

Run `carthage update` to build the framework and drag the built `Seam.framework` into your Xcode project.

## Usage

Add a Store type of ```SeamStoreType``` to a NSPersistentStoreCoordinator in your CoreData stack:

``` swift
let persistentStoreCoordinator = NSPersistentStoreCoordinator(managedObjectModel: yourModel)
let seamStore = try persistentStoreCoordinator.addPersistentStoreWithType(SeamStoreType, 
                                                                          configuration: nil, 
                                                                          URL: url, options: nil) as? Store
```
Observe the following two Notifications to know when the Sync Operation starts and finishes:

``` swift

NSNotificationCenter.defaultCenter().addObserver(self, selector: "didStartSyncing:",
                                                name: SMStoreDidStartSyncingNotification,
                                                object: seamStore)
NSNotificationCenter.defaultCenter().addObserver(self, selector: "didFinishSyncing:",
                                                name: SMStoreDidFinishSyncingNotification,
                                                object: seamStore)                                               

func didStartSyncing(notification: NSNotification) {
  // Prepare for new data before syncing completes
}
  
func didFinishSyncing(notification: NSNotification) {
  // Merge Changes into your context after syncing completes
  mainContext.mergeChangesFromStoreDidFinishSyncingNotification(notification)
}
  
```

Finally call sync whenever and wherever you want:

```swift
seamStore.sync()
```

## Attributes

All CloudKit Attribute

| CloudKit  | CoreData |
| ------------- | ------------- |
| NSDate    | NSDate
| NSData | NSData
| NSString  | NSString   |
| NSNumber | NSNumber |
| CKReference | NSManagedObject |
| CKAsset | Transformable |
| CLLocation | Transformable |

### Transformable Attributes

CKAsset and CLLocation can be used in your CoreData model as Transformable attributes.

1. To use **CKAsset** set **Transformable** as AttributeType and **CKAssetTransformer** as value transformer name for the attribute.

![](https://cloud.githubusercontent.com/assets/3306263/11773251/f342fd36-a248-11e5-8b55-519400fdb600.png)

2. To use **CLLocation** set **Transformable** as AttributeType and **CLLocationTransformer** as value transformer name for the attribute.

![](https://cloud.githubusercontent.com/assets/3306263/11773252/f3459564-a248-11e5-89eb-197c32ef245a.png)


## Relationships

| CoreData Relationship  | Translation on CloudKit |
| ------------- | ------------- |
| To - one    | To one relationships are translated as CKReferences on the CloudKit Servers.|
| To - many    | To many relationships are not explicitly created. Seam only creates and manages to-one relationships on the CloudKit Servers. <br/> <strong>Example</strong> -> If an Employee has a to-one relationship to Department and Department has a to-many relationship to Employee than Seam will only create the former on the CloudKit Servers. It will fullfil the later by using the to-one relationship. If all employees of a department are accessed Seam will fulfil it by fetching all the employees that belong to that particular department.|

<strong>Note :</strong> You must create inverse relationships in your app's CoreData Model or Seam wouldn't be able to translate CoreData Models in to CloudKit Records. Unexpected errors and curroption of data can possibly occur.

## Sync

Seam keeps the CoreData store in sync with the CloudKit Servers. It let's you know when the sync operation starts and finishes by throwing the following two notifications.
- SMStoreDidStartSyncOperationNotification
- SMStoreDidFinishSyncOperationNotification

### What it does support

Seam supports only user's CloudKit `Private Database` at this time. It creates and uses a custom zone to store data and fetch changes from the server.

### What it does not support

CloudKit `Public Database` and here are the two reasons why, straight from the docs.

1. [The disadvantage of using the default zone for storing records is that it does not have any special capabilities. You cannot save a group of records to iCloud atomically in the default zone. Similarly, you cannot use a CKFetchRecordChangesOperation object on records in the default zone.](https://developer.apple.com/library/prerelease/ios/documentation/CloudKit/Reference/CKRecordZone_class/index.html#//apple_ref/occ/clm/CKRecordZone/defaultRecordZone)

2. [ You cannot create custom zones in a public database.](https://developer.apple.com/library/prerelease/ios/documentation/CloudKit/Reference/CKRecordZone_class/index.html#//apple_ref/c/tdef/CKRecordZoneCapabilities)

## Getting Started 
Download the demo project. Run it and see the magic as it happens.

## Installation
CocoaPods is the recommended way of adding Seam to your project.

Add this `'Seam', '~> 0.6'` to your pod file.

## Credits
Seam was created by [Nofel Mahmood](http://twitter.com/NofelMahmood)

## Contact 
Follow Nofel Mahmood on [Twitter](http://twitter.com/NofelMahmood) and [GitHub](http://github.com/nofelmahmood) or email him at nofelmehmood@gmail.com

## License
Seam is available under the MIT license. See the LICENSE file for more info.
