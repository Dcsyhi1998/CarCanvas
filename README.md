<p align="center">
  <img src="docs/app-icon.png" alt="CarCanvas" width="128" height="128" />
</p>

<h1 align="center">CarCanvas</h1>

<p align="center">
  <a href="README.ja.md">日本語版はこちら</a>
</p>

<p align="center">Overwrite CarPlay wallpaper caches with your own images.</p>

CarPlay reads a previously created cache file when it starts. This app replaces that file with a custom CPBitmap.

## Features

- Create local CPBitmap wallpapers from photos
- Export history with previews
- Overwrite the CarPlay wallpaper cache (select Dark / Light individually)
- View and manage cache files within the app
- Setup guide for first-time users

## Installation

Download the IPA file from [Releases](https://github.com/Dcsyhi1998/CarCanvas/releases).

CarCanvas **cannot be installed the normal way**.

This app relies on the `com.apple.mobile.MobileHouseArrest` identity and accesses another app’s container (CarPlayWallpaper). It will **not work** with the following installation methods:

- Installation from the App Store
- Installation using an Xcode personal/development certificate
- Sideloading with **AltStore** (regardless of whether you use the free or paid tier)

You must **sign the IPA with an Enterprise / In-House certificate** before installing it.

If the IPA is unsigned or merely re-signed for AltStore, it will not receive the required permissions or identity and will not be able to access the wallpaper cache.

There are also free apps such as **ESign** for signing and installing with an enterprise certificate. The specific installation procedure differs depending on the tool and environment, so **please research it yourself**. This README does not cover detailed installation instructions.

Once CarCanvas is installed, you can freely change your CarPlay wallpaper.

## Credits / Acknowledgments

This project was made possible based on the source code and research from [0xjohnnydev/FilzaSlop](https://github.com/0xjohnnydev/FilzaSlop). Without the container access through MobileHouseArrest and related implementation demonstrated by FilzaSlop, CarCanvas would not have been able to manage the CarPlayWallpaper cache. Thank you.

> Special thanks to 0xjohnny for FilzaSlop and related research:
> https://github.com/0xjohnnydev/FilzaSlop

FilzaSlop is a fork of the FilzaJailedDS series and provides access to app data containers and other features. The CarCanvas IPA similarly relies on the `com.apple.mobile.MobileHouseArrest` identity.

## How it works

CarPlay does not generate wallpapers from scratch every time. When it starts, it reads previously created cache files.

The process is as follows:

1. First, select wallpapers in CarPlay to create the cache files.
2. Save your own wallpaper as a CPBitmap in CarCanvas.
3. From the history, overwrite an existing cache using the same file name.
4. **Turn off and restart your iPhone** (only once is required).
5. After restarting, CarPlay reads the cache again and displays the overwritten wallpaper.

If no cache exists, there is nothing to overwrite. You must first create the cache in CarPlay.

The wallpaper will not be applied immediately after overwriting the cache. The CarPlay process continues running in the background and keeps using the old cache. The process may eventually terminate if you wait, but the required waiting time is unknown. The reliable method is to turn off and restart your iPhone. Because this is more reliable, it is the method used in these instructions. You only need to restart once after overwriting all the cache files.

## Preparation: Create the Cache in CarPlay

Open **Wallpaper in the CarPlay settings** and select the **six patterned wallpapers** in the top and middle rows, starting from the upper left. Each time you select one, the corresponding Dark / Light cache files are created.

![CarPlay wallpaper picker](docs/carplay-wallpaper-picker.png)

| Order | Position      | Color           | Cache files                                                                     |
| ----- | ------------- | --------------- | ------------------------------------------------------------------------------- |
| 1     | Top-left      | Blue            | `CARWallpaperBlue-Dark-14.cpbitmap`<br>`CARWallpaperBlue-Light-14.cpbitmap`     |
| 2     | Top-center    | Purple          | `CARWallpaperPurple-Dark-14.cpbitmap`<br>`CARWallpaperPurple-Light-14.cpbitmap` |
| 3     | Top-right     | Gray            | `CARWallpaperGray-Dark-14.cpbitmap`<br>`CARWallpaperGray-Light-14.cpbitmap`     |
| 4     | Middle-left   | Green           | `CARWallpaperGreen-Dark-14.cpbitmap`<br>`CARWallpaperGreen-Light-14.cpbitmap`   |
| 5     | Middle-center | Brown           | `CARWallpaperBrown-Dark-14.cpbitmap`<br>`CARWallpaperBrown-Light-14.cpbitmap`   |
| 6     | Middle-right  | Red             | `CARWallpaperRed-Dark-14.cpbitmap`<br>`CARWallpaperRed-Light-14.cpbitmap`       |
| —     | (sometimes)   | Black (pattern) | `CARWallpaperBlack-Dark-14.cpbitmap`<br>`CARWallpaperBlack-Light-14.cpbitmap`   |
| —     | Bottom-left   | Black (solid)   | **Not created**                                                                 |
| —     | Bottom-center | Dark gray       | **Not created**                                                                 |
| —     | Bottom-right  | Brown (solid)   | **Not created**                                                                 |

The three solid-color wallpapers in the bottom row (black, dark gray, and brown) are treated as colors rather than images. Selecting them does not create cache files, so they cannot be overwritten.

Preparation is complete once you have selected each of the six patterned wallpapers once. You do not need to repeat this process unless the cache is deleted.

The `-14` suffix in the file name may differ depending on the environment. On the CarCanvas export screen, the names found on the actual device are used as they are.

## Dark and Light

Each patterned wallpaper comes as a pair: **Dark** and **Light**.

| File                  | Used when                                                                         |
| --------------------- | --------------------------------------------------------------------------------- |
| `*-Dark-14.cpbitmap`  | At night or when the appearance is set to dark mode                               |
| `*-Light-14.cpbitmap` | In the morning or in bright conditions (when the appearance is set to light mode) |

This switching occurs only when the CarPlay appearance mode is set to **Automatic**.
If it is always set to dark, only the Dark version is used and the Light version is not loaded.

If you set the same wallpaper for both Dark and Light, the image will remain the same even when the time of day changes. If you set different images, they will automatically switch, such as **Wallpaper A in the morning and Wallpaper B** at night.

## How to Use CarCanvas

### 1. Save Your Wallpaper Locally

1. Open CarCanvas
2. Open **Wallpaper**
3. Select the image you want to use
4. Press Create CPBitmap Locally

The image is saved in the app as a CPBitmap and remains in **History**. The CarPlay display does not change at this point.

### 2. Export to the CarPlay Cache

1. Open **History**
2. Tap the item you just saved
3. Select **Export to CarPlay Wallpaper**
4. A list of CarPlay cache files will appear. Select the Dark / Light file you want to overwrite
5. Execute **Overwrite**

The Dark / Light versions in the history are exported to their corresponding Dark / Light cache files. Files with the same name are always overwritten.

If you want to use the same image, export the same history item to both the Dark and Light versions of the pattern you want to use (blue, purple, gray, etc.). If you want different images for morning and night, export Wallpaper A to Light and Wallpaper B to Dark separately. If the CarPlay appearance is set to Automatic, they will switch according to the brightness.

### 3. Restart the iPhone (once)

After overwriting the cache with your own wallpaper, **you must turn off and restart your iPhone**. Doing this once will reliably terminate the CarPlay process, and the new cache should be loaded after startup.

If you wait, the process will eventually terminate, but it is not known whether you need to wait minutes or hours.

Therefore, turning off the device is more reliable.

## Additional Information

On the **CarPlay Wallpaper** screen in CarCanvas, you can directly view the cache folder that will be overwritten. You can also import, rename, duplicate, and delete files.

## Related

| Name                                                      | Role                                                                                                        |
| --------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| **CarCanvas** (this app)                                  | View and overwrite the CarPlay wallpaper cache                                                              |
| **[FilzaSlop](https://github.com/0xjohnnydev/FilzaSlop)** | Open-source project by 0xjohnny that provided the foundation for container access and related functionality |

## Disclaimer

CarCanvas relies on private system behavior and a special identity. It may stop working after an iOS or CarPlay update. Use it at your own risk. Enterprise certificates and sideloading may violate Apple’s terms or the policies of your environment. Users are responsible for making that determination and accepting the consequences.
