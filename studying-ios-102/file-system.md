## [apple documentation](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)

- APFS replaces HFS+ as the default file system for iOS 10.3 and later

- During installation of a new app, the installer creates a number of container directories for the app inside the sandbox directory. Each container directory has a specific role. 

Common directories of an App:
`AppName.app`
> The app’s bundle
You cannot write to this, it is signed at installation time

`Documents`
> Backed up to iTunes and iCloud

`Documents/Inbox`
> Files your app was asked to open by outside entities

`Library/`
> Files that are not user data files
Apps commonly use “Application support” and “caches” subdirectories

`tmp/`
> used to write temporary files that do not need to persist between launches

- `Documents/` and `Application Support` are backed up by default

- An iOS app can designate files that it wants to be encrypted on disk. When the user unlocks a device containing encrypted files, the system creates a decryption key that allows the app to access its encrypted files. When the user locks the device, though, the decryption key is destroyed to prevent unauthorized access to the files.

## [understanding the iOS File System](https://medium.com/@lucideus/understanding-the-ios-file-system-eee3dc87e455)

- the root directory `/`
    - when jailbroken, all files under `/` have read write access
    - directories similar to MacOS: `Applications`, `Library`, `System`, `User`
    - unix directories: `bin`, `boot`, `dev`, `etc`, `lib`, `mnt`, `sbin`, `tmp`, `usr`, `var`
    - unique directories: `private`, `cores`
    - files with the name `.file` may be used for file integrity checks

- the home directory `/var/root -> /private/var/root`
    - contains two directories by default: `Application Support` and `Library`

- directories in the `PATH`
    - \$PATH: `/usr/bin:/bin:/usr/sbin:/sbin`

- iOS Applications
    - an iOS app has access to the following directories/components:
        - pre installed native apps
        - `/Applications/AppName.app`
        - `/var/containers/Bundle/Application/AppUUID`
        - `/var/mobile/Containters/Data/Application/AppUUID`
        - `/var/mobile/Containers/Shared/AppGroup/AppUUID`
        - `/var/Keychains/keychain-2.db`
        - `UIPasteboard`

- `/Applications`
    - contains all the preinstalled apps
    - cannot be deleted (normally)

- `/var/containers/Bundle/Application`
    - where all iOS applications installed from the App Store live
    - `/var/containers/Bundle/Application/{uuid}` where uuid is the UUID of an application. UUID is unique for each application and always changes for a fresh installation of that application.

- Data directory: `/var/mobile/Containers/Data/Application`
    - This directory stores the local data of all the applications
    - can only be accessed by the owner application because of sandboxing

- Shared data directory: `/var/mobile/Containers/Shared/AppGroup`
    - all the installed apps may not have an entry in here
    - data shared by a group of applications or their own extensions
    - `/var/mobile/Containers/Shared/AppGroup/{uuid}` where uuid is the UUID of the application group.

- iOS Keychain: `/var/Keychains`
    - an SQLite database file that contains the items stored by the iOS keychain for any application including the WiFi passwords, iTunes apple id etc.
    - name of the keychain is `keychain-2.db`

## [iphone wiki](https://www.theiphonewiki.com/wiki//)

### the `/` directory

- `.ba`
- `.mb`
- `AppleInternal`
    - This is a folder used on Apple Internal firmwares only
    - It contains bundles and applications used for testing, like `SkankPhone` or `SwitchBoard.app`.
        - SkankPhone was a notable part of Non-UI firmwares from versions 1.0 to 5.1. It's last appearance was on prototypes running 5.1. Versions 6.0 and above no longer feature SkankPhone, for unknown reasons.
        - SkankPhone does not depend on SpringBoard, SkankPhone can live with or without it. It is not like a regular UIKit application, in this way, it is not linked with UIKit and uses it's own custom framework. SkankPhone's framework located in it's application bundle named SkankKit, this is small UIKit replacement. SkankPhone is launched by a daemon. Somewhere in this process SkankPhone unloads SpringBoard's daemon, this kills SpringBoard while giving SpringBoard no way to respawn.
    - subdirectories:
        - `Applications`
            - [a ton of stuff](https://www.theiphonewiki.com/wiki/Apple_Internal_Apps)
        - `CoreOS`
        - `Developer`
        - `Diags`
            - AMCSS
            - AudioTests
            - Baseband
            - Battery
            - Bluetooth
            - Debug
            - Earthbound
            - FactoryInfo
            - Fonts
            - Grape
            - HighlandPark
            - ISP
            - Inferno
            - LCMPatterns
            - Logs
            - Mesa
            - Oscar
            - RAMDisk
            - Scripts
            - Diags/Tests
            - WiFiFirmware
            - bin
            - purpleskank
        - `Library`
            - awdd
            - BulletinBoardPlugins
            - Bundles
            - CacheDelete
            - CloudServices
            - Conference
            - CoreProfile
            - Frameworks
            - LaunchDaemons
            - LocationBundles
            - PerlModules
            - PreferenceBundles
            - Preferences
            - WiFi
        - `Lockdown`
        - `Restore`
        - `Utilities`
        - `XCTests`
- `Applications`
- `bin`
> Folder that contains GNU Coreutils. The GNU Core Utilities are the basic file, shell and text manipulation utilities of the GNU operating system. These are the core utilities which are expected to exist on every operating system.
- `boot`
> not present in newer iOS'. used to store OTA update data, was the mountpoint for the upgrade partition
- `cores`
- `dev`
> Device Nodes are here, as with any other \*nix system. The directory is read-only as its files don't actually exist and are transparently handled by the kernel.
- `Developer`
> This folder contains nothing by default. However, if you hook your iPhone up to Xcode, and click the "Use For Development" button, the content of 'DeveloperDiskImage.dmg' gets decompressed by /usr/libexec/mobile_image_mounter into here.
- `etc`
> config files
- `Library`
> This folder contains files and executables required by all users and application
- `lib`
> This folder is specified by the FHS as a place for the "shared library images needed to boot the system and run the commands in the root filesystem (i.e. by binaries in /bin and /sbin)." I
- `mnt`
> This folder is specified by the FHS as a place where "the system administrator may temporarily mount a filesystem as needed." Despite that, this folder is not used by iOS (as far as can be told); ramdisks are mounted in /sbin instead. It could possible be used for the Camera Connection Kit, but this is unverified.
- `private`
- `sbin`
> This folder is where ramdisks are uploaded. It contains important files like mount, launchd, and fsck
- `System`
> This folder contains the bulk of the root partition. It contains Frameworks among many other things.
- `tmp`
- `User`
> This is the home directory of the non-root user (mobile). User data and media are stored in this directory (for example, iTunes music resides in Media/iTunes Control/Music).
- `usr`
> This folder contains static data, as defined by the HFS directory tree standard.
- `var`
> This folder is the mount point for /dev/disk0s2 (/dev/disk0s1s2 on modern iOS versions), which is the device's user/data partition. This deviates from the Filesystem Hierarchy Standard which places this at /var, however, a sym-link is placed at /var that redirects to here, so it is at least somewhat compliant.
