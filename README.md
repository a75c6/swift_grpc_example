gRPC Swift Template

Create blank xcode project.

Clone the following ensuring there are no duplicate repositories.

https://github.com/grpc/grpc-swift

https://github.com/apple/swift-protobuf

Need to use the following forked library:

https://github.com/namsoila/CZlib

In the grpc-swift folder, edit the Package.swift file as follows to utilize the forked version which avoid the building of a target using macOS as the baseSDK:

```
dependencies: [
    .Package(url: "https://github.com/namsoila/CZlib.git", majorVersion: 0, minor: 5),
  ]
```

This is to avoid the following error:

```
target specifies product type 'com.apple.product-type.tool', but there's no such product type for the 'iphoneos' platform
```

Run `make` in the home directory of grpc-swift to create SwiftGRPC.xcodeproject.

Add SwiftGRPC.xcodeproject to the blank xcode project below the project but above the primary folder.

In swift-protobuf, take the SwiftProtobuf.xcodeproject file and also place that in the project but above the primary folder and the previosly added xcode project.

Now you need to generate and add the protobuf files.

Run `make` in the Plugin directory of grpc-swift. This will generate `protoc-gen-swift` and `protoc-gen-swiftgrpc`.

These two need to be added to your $PATH with /usr/local/bin using `sudo cp file_name /usr/local/bin`.

Create a .proto file which uses the following as an example and includes a package indication:

https://github.com/grpc/grpc-swift/blob/master/Examples/Echo/echo.proto

Here is the command which is run locally in a directory with the .proto file:
```
protoc ./file.proto --swift_out=. --swiftgrpc_out=.
```
These files now need to be added to the project. Create an empty group under the primary folder of the xcode project.

Copy the files to that folder via drag and drop and allow for copying if requried.

Now you must add the frameworks to the project. Using the top level project target, add all the frameworks (CgRPC, gRPC, SwiftProtobuf for iOS, BoringSSL, and Czlib) to both the linked frameworks and the embedded binaries.

You must add them to the embedded binaries to avoid the following error:

https://stackoverflow.com/questions/24333981/ios-app-with-framework-crashed-on-device-dyld-library-not-loaded-xcode-6-beta

Now in order to have the headers be recognized, you must have them be directly referenced to the exact location on your file system. This can be done via a general structural reference or through a direct reference specific to the folder on your file system:
```
../../../Sources/CgRPC/include
```
Or a specific reference such as:
```
/User/Device/Downloads/grpc-swift-master/Sources/CgRPC/include
```
All of these items as being added in the Build Settings > Header Search Paths of the the main target.
