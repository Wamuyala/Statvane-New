# Testing the platform in GitHub Codespaces

The platform installs and starts by itself when the codespace opens (the first time takes a few minutes).

1. Open the **Ports** tab at the bottom and click the globe icon next to **Platform (8000)** to open it.
2. Create an account, create a project, and click **Add the synthetic demo dataset**.

**Share it with others:** in the Ports tab, right-click port 8000 → **Port visibility** → **Public**, then send the
link. Survey links and QR codes already use this address.

**If the page doesn't load:** in the terminal run `bash .devcontainer/start.sh`. The log is in `/tmp/platform.log`.

**Stop it to save your free hours:** github.com/codespaces → the three dots next to this codespace → **Stop**.
Your test data is kept until you delete the codespace.

This is for testing only, not for real participants' data. See docs/DEPLOYMENT.md to put it online properly.
