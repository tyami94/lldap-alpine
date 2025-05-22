# Alpine Linux packaging for lldap

This builds and packages v0.6.1 with a backported patch to allow custom asset paths (required to package the software well).

This worked last time I tried it, but the patch it used was not the final patch that was accepted upstream. A few minutes ago, I backported the final patch to this repo, and it should work, but I have not tested it. If it doesn't, open an issue and I'll fix it up.

Good luck!

# Usage

1. Install `alpine-sdk`
2. Run abuild-keygen to generate a keypair
3. Copy your public key into `/etc/apk/keys/`
4. Clone this repo and cd into it
5. Run `abuild`
6. Install the compiled package with apk

If you're confused, more in-depth instructions for building the package are available on the Alpine wiki. If you're *really* confused, open an issue, and I'll improve the docs ;-)
