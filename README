
<div align="center">

# FPrintD

*FPrintD is part of the **[FPrint][Website]** project.*

<br/>

[![Button Website]][Website]
[![Button Documentation]][Documentation]

[![Button Supported]][Supported]
[![Button Unsupported]][Unsupported]

[![Button Contribute]][Contribute]
[![Button Contributors]][Contributors]

</div>

## History

**FPrintD** was developed as the D-Bus daemon companion to **LibFPrint**,
offering fingerprint reader functionality over D-Bus to desktop applications
and PAM authentication modules.

## Goal

The ultimate goal of the **FPrint** project is to make fingerprint scanners
widely and easily usable under common Linux environments.

## License

`Section 6` of the license states that for compiled works that use
this library, such works must include **FPrintD** copyright notices
alongside the copyright notices for the other parts of the work.

Licensed under the **GPL version 2** or any later version (see COPYING).

## Get in *touch*

 - [IRC] - `#fprint` @ `irc.oftc.net`
 - [Matrix] - `#fprint:matrix.org` bridged to the IRC channel
 - [MailingList] - low traffic, not much used these days

<br/>

<div align="right">

[![Badge License]][License]

</div>


## ElanSPI Enhancement Fork

This fork adds support for the `FP_DEVICE_RETRY_DIFFERENT_AREA` retry code
introduced in the **[companion libfprint fork][libfprint-fork]** for the
**ElanTech ELAN7001 SPI** fingerprint sensor found in **ASUS VivoBook X515EA**
and similar laptops.

### Why This Exists

The companion libfprint fork adds a diversity check during enrollment that
rejects swipes too similar to previously stored ones, requesting a different
finger area. The upstream fprintd has no knowledge of this retry code, so
diversity-driven retries produce the misleading `"enroll-retry-scan"` message.

### Changes

1. **`src/device.c`** — Map `FP_DEVICE_RETRY_DIFFERENT_AREA` (enum value 5)
   to descriptive D-Bus result strings:

   ```c
   case FP_DEVICE_RETRY_DIFFERENT_AREA:
     return "verify-different-area";
   ```

   ```c
   case FP_DEVICE_RETRY_DIFFERENT_AREA:
     return "enroll-different-area";
   ```

2. **`tests/dbusmock/fprintd.py`** — Register the new result strings in
   the test mock's valid status lists:

   ```python
   VALID_RESULT_VERIFY_STATUSES.extend(['verify-different-area'])
   VALID_RESULT_ENROLL_STATUSES.extend(['enroll-different-area'])
   ```

### How This Was Built

This fork was developed interactively using **opencode/big-pickle**
— an AI coding assistant — by a non-C-developer user with no GObject
or D-Bus expertise. The AI analyzed the libfprint source, proposed the
retry code handling, and iterated through build-test cycles to get the
full stack (enrollment → diversity retry → proper D-Bus result string)
working end-to-end.

The full development story, including all debugging and parameter tuning,
is documented in the companion libfprint README.

### Building

fprintd must be built against the companion libfprint fork (which defines
`FP_DEVICE_RETRY_DIFFERENT_AREA`). Use `PKG_CONFIG_PATH` to point meson
at the libfprint build directory:

```bash
cd fprintd
PKG_CONFIG_PATH=$PWD/../libfprint/builddir/meson-private \
  meson setup builddir
ninja -C builddir
```

### Testing

**D-Bus constraint:** Only root may own the `net.reactivated.Fprint` name.
The stock daemon auto-starts via D-Bus activation and loads the system
libfprint. To test:

1. Replace the system libfprint .so with the custom build:
   ```bash
   sudo cp ../libfprint/builddir/libfprint/libfprint-2.so.2.0.0 /usr/lib/
   sudo ldconfig /usr/lib
   ```

2. Enroll (stock daemon loads custom lib, retries show as
   `"enroll-retry-scan"`):
   ```bash
   fprintd-delete "$USER"
   fprintd-enroll
   ```

3. For the proper `"enroll-different-area"` strings, run the custom daemon
   as root:
   ```bash
   sudo systemctl stop fprintd
   sudo LD_LIBRARY_PATH=$PWD/../libfprint/builddir/libfprint \
     G_MESSAGES_DEBUG=all \
     $PWD/builddir/src/fprintd
   ```

On reboot, everything reverts to stock.

### Related Repos

- **libfprint fork**: https://github.com/dev-AbhinavNair/libfprint
  (image enhancement, diversity checking, and driver tuning)


<!----------------------------------------------------------------------------->

[Documentation]: https://fprint.freedesktop.org/libfprint-dev/
[Contributors]: https://gitlab.freedesktop.org/libfprint/fprintd/-/graphs/master
[Unsupported]: https://gitlab.freedesktop.org/libfprint/wiki/-/wikis/Unsupported-Devices
[Supported]: https://fprint.freedesktop.org/supported-devices.html
[Website]: https://fprint.freedesktop.org/
[MailingList]: https://lists.freedesktop.org/mailman/listinfo/fprint
[IRC]: ircs://irc.oftc.net:6697/#fprint
[Matrix]: https://matrix.to/#/#fprint:matrix.org

[Contribute]: ./HACKING.md
[License]: ./COPYING

[libfprint-fork]: https://github.com/dev-AbhinavNair/libfprint


<!---------------------------------[ Badges ]---------------------------------->

[Badge License]: https://img.shields.io/badge/License-GPL2.0-015d93.svg?style=for-the-badge&labelColor=blue


<!---------------------------------[ Buttons ]--------------------------------->

[Button Documentation]: https://img.shields.io/badge/Documentation-04ACE6?style=for-the-badge&logoColor=white&logo=BookStack
[Button Contributors]: https://img.shields.io/badge/Contributors-FF4F8B?style=for-the-badge&logoColor=white&logo=ActiGraph
[Button Unsupported]: https://img.shields.io/badge/Unsupported_Devices-EF2D5E?style=for-the-badge&logoColor=white&logo=AdBlock
[Button Contribute]: https://img.shields.io/badge/Contribute-66459B?style=for-the-badge&logoColor=white&logo=Git
[Button Supported]: https://img.shields.io/badge/Supported_Devices-428813?style=for-the-badge&logoColor=white&logo=AdGuard
[Button Website]: https://img.shields.io/badge/Homepage-3B80AE?style=for-the-badge&logoColor=white&logo=freedesktopDotOrg
