# Kubespray offline image exports

This repository exports **each** line of `images.list` as a separate GitHub Actions artifact.

## Run
1. Push `.github/workflows/export-images.yml` and `images.list` to GitHub (keep the folder structure).
2. Open **Actions** > **Export Kubespray images individually** > **Run workflow**.
3. After the run, download each artifact from the workflow run's **Artifacts** section.
4. Extract each downloaded artifact ZIP. Each contains a `.tar` (Docker image archive) and `.image.txt` (the original image reference).
5. On an offline machine with Docker, import each tar using `docker load -i <filename>.tar`.

If you use containerd, you can instead use `sudo ctr -n k8s.io images import <filename>.tar` (the import namespace and image references must match your runtime configuration).

**No SHA256 checksum files are generated.** GitHub Actions may generate its own artifact integrity metadata independently.

This exports **all images from the supplied list**, including alternative CNIs and optional add-ons. It does **not** export kubeadm/kubelet/kubectl binaries or other `files.list` resources.

Notes:
- The workflow explicitly pulls Linux AMD64 images. Adjust the platform if the target cluster uses a different CPU architecture.
- Pulls require source registries to be accessible to GitHub-hosted runners; rate limits or missing tags may cause individual jobs to fail.
- Matrix jobs run independently (`fail-fast: false`), and the artifact name includes its original list number.
- Artifacts are retained for 30 days unless your repository/org policy reduces this limit.
- Downloading and extracting all ZIP files still requires importing all image TARs into an internal registry or runtime. Merely loading the images on one node does not configure Kubespray for offline deployment.
