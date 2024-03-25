# Azure Blob Storage issue #

This repository contains info on how to reproduce an issue I experience with
Azure Storage Blob from Azure Python SDK.

## Issue

When experiencing timeouts while downloading a blob with Azure Python SDK the
whole download will fail when the network is brought back to normal even if
there are retries left. I have not been able to reproduce this with uploads.

## Reproduce

1. Add a sufficiently large file (I use a 1.5GiB file) to your azure storage
   account at
   `https://<ACCOUNT>.blob.core.windows.net/<CONTAINER>/<BIG_BLOB>`.
2. Login with `az login`.
3. Create a fresh virtual environment and install the dependencies with:
   ```console
   $ pip install --requirement=./requirements.txt
    ```
4. Run
   ```console
   $ ./azure-storage-test -a <ACCOUNT> -c <CONTAINER> <BIG_BLOB>
   $ # E.g. ./azure-storage-test -a mytestaccount -c test my-big-blob.zip
   ```
5. Once the download has run for a little while, in a separate terminal, run
   ```console
   $ ./network-latency on <NET-DEVICE> # E.g eth0 or enp0s13f0u3u4 
   ```
   This adds a 100s network latency to the download to make sure it times out.
6. Watch the download starting to print out that it will retry a little later.
7. Wait until the second round of retries.
8. Disable the network latency:
   ```console
   $ ./network-latency off <NET-DEVICE>
   ```
9. Watch the download fail even though we have retries left and the network is
   back to normal again.

## Example log

See [this example log](./log) for how this looks like at my computer.
