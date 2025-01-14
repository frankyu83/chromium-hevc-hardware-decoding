# enable-chromium-hevc-hardware-decoding

A guide that teach you build a custom version of chrome on macOS / Windows / Linux that supports hardware / software HEVC decoding.


##### English | [简体中文](./README.zh_CN.md)

## Get release build?

[Click here get pre-built release](https://github.com/StaZhu/enable-chromium-hevc-hardware-decoding/releases/tag/103.0.5011.0)

## What's the supported HEVC profile?

#### Hardware Decoding Support

1. HEVC Main (Up to 8192x8192 pixels)
2. HEVC Main10 (Up to 8192x8192 pixels)

#### Software Decoding Support

Actually all the profile that FFmpeg supports should be supported here, like:

1. HEVC Main Still Picture
2. HEVC Rext
3. HEVC SCC

## What's the OS requirement?

#### Hardware Decoding Requirement

macOS 11.0+

Windows 8+

Linux + Vaapi (Not tested)

#### Software Decoding Requirement

All OS. like Windows 7, macOS 10.12, etc...

## HDR Supports? (Compared with Edge/Safari) 

|                  | PQ (SDR Screen) | PQ (HDR Screen) | HLG (SDR Screen) | HLG (HDR Screen) |
| :-------------- | :------------- | :------------- | :-------------- | :-------------- |
|  Chromium macOS  |     ✅ (EDR)      |        ✅        |      ✅ (EDR)      |        ✅         |
| Chromium Windows |        ✅        |        ✅        |        ✅         |        ✅         |
|  Chromium Linux  |   Not Tested    |   Not Tested    |    Not Tested    |    Not Tested    |
|   Edge Windows   |        ❌        |        ✅        |        ✅         |        ❌         |
|   Safari macOS   |     ✅ (EDR)      |        ✅        |      ✅ (EDR)      |        ✅         |

## How to verify HEVC hardware support is enabled?

1. Open `chrome://gpu`, and search `Video Acceleration Information`, you should see **Decode hevc main** field and **Decode hevc main 10** field present if hardware decoding is supported.
2. Open `chrome://media-internals` and play some HEVC video if the decoder is `VDAVideoDecoder` or `D3D11VideoDecoder` or `VaapiVideoDecoder` that means the video is using hardware decoding, and if the decoder is `FFMpegVideoDecoder` that means  the video is using software decoding.
3. Open `Activity Monitor` on Mac and search `VTDecoderXPCService`, if the cpu usage larger than 0 when playing video, that means hardware decoding is being used.
4. Open `Windows Task Manager` on Windows and switch to `Performance` -`GPU`, if `Video Decoding` usage larger than 0 when playing video,  that means hardware decoding is being used.

## How to Build?

1. Follow [the official build doc](https://www.chromium.org/developers/how-tos/get-the-code/) to prepare the build environment then fetch the source code to tag `103.0.5011.0` (newer tag or `main` branch should also work if no code conflict with the following patch).
2. (Optional) To enable HEVC software decoding: switch to `src/third_party/ffmpeg` dir, then execute `git am /path/to/add-hevc-ffmpeg-decoder-parser.patch`.
3. (Optional) To enable other HEVC profiles (non main / main10 profiles): switch to `src` dir, then execute `git am /path/to/remove-main-main10-profile-limit.patch`.
4. (Optional) To default enable hardware decode: switch to `src` dir, then execute `git am /path/to/enable-hevc-hardware-decoding-by-default.patch`.
5. (Optional) To remove chrome switch passing: switch to `src` dir, then execute `git am /path/to/remove-clear-testing-args-passing.patch`.
6. If you are using `x64` arch cpu, run `gn gen out/Release64 --args="is_component_build = false is_official_build = true is_debug = false ffmpeg_branding = \"Chrome\" target_cpu = \"x64\" proprietary_codecs = true media_use_ffmpeg = true enable_platform_encrypted_hevc = true enable_platform_hevc = true enable_platform_hevc_decoding = true"`,  you can change target_cpu to `x86` , `arm64` , `arm` if you want to build other cpu archs.
7. Run `autoninja -C out/Release64 chrome` to start the build.
8. Run `./out/Release64/Chromium.app/Contents/MacOS/Chromium --args --enable-clear-hevc-for-testing --enable-features=VideoToolboxHEVCDecoding` to open chromium if you are using macOS.
9. Create a desktop shortcut and passing the args like `C:\Users\Admin\Desktop\Chromium\chrome.exe --enable-clear-hevc-for-testing --enable-features=D3D11HEVCDecoding` then double click the desktop shortcut to open chromium if you are using Windows.

## Change Log

`2022-4-27` Replace to `git am` patch

`2022-4-24` Support chinese readme

`2022-4-21` Add Crbug trace

`2022-4-20` Modify readme

`2022-4-19` Initial commit

## Trace Crbug

##### [Windows](https://crbug.com/1286132)

##### [macOS](https://crbug.com/1300444)

## License

MIT
