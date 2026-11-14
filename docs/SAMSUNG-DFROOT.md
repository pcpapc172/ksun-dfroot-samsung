# Samsung DFRoot compatibility port

Target: Galaxy A16 4G SM-A165F, firmware A165FXXSADZF2, arm64 kernel 6.12.38-android16-5, 4 KiB pages (user supplied). This branch ports the Samsung compatibility implementation from diabl0w/KernelSU samsung-v2.2 (b38f4096) onto KernelSU Next dev (3987e4d5). It does not provide a new root exploit.

## Scope

- Runtime Samsung KDP detection, protected credential allocation/installation, and 6.12 credential reference-count handling.
- Runtime RKP detection and arm64 branch/trampoline handling, including the v2.2 BTI/PAC and bulk patch fixes.
- Samsung DEFEX compatibility from the same release.
- Integration with current KernelSU Next initialization, credential release and SELinux hooks. Late-load's initial credential installation also uses the KDP-aware wrapper.
- KernelSU Next manager identity/signature checks remain enabled and unchanged. No root hiding features or device boot images are configured by this port.

Source and attribution: https://github.com/diabl0w/KernelSU/releases/tag/samsung-v2.2 . Imported kernel sources retain their upstream license terms; see git history for the original implementation. The older `samsung-lateload` branch in this fork is a separate implementation and was not merged into this branch.

## DFRoot interface

DFRoot's bootstrap stages the selected manager's native libksud.so, invokes `ksud late-load --package-name <selected-package>`, and optionally invokes `ksud soft-reboot`. KernelSU Next already provides these commands. DFRoot must select the APK built with this branch's daemon and embedded patched kernel module; changing only the Manager UI does not apply these kernel changes. Do not install a module built for a different KMI.

## Build and validation

The `Build Samsung A16 LKM` manual workflow builds the android16-6.12 module through the repository's existing DDK workflow. The existing manager/release workflows must also package that patched module into ksud. A locally signed APK needs matching manager signature parameters in the module build; keep the signing key private.

Compilation and device validation are tracked separately. A successful module build does not establish A16 runtime compatibility. No phone changes, exploit attempts, or flashing are part of this source port. Kernel modules from this branch are experimental until tested on the exact target firmware. Module unloading and recovery paths require separate validation.
