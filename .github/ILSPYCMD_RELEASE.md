# ilspycmd NativeAOT releases

The `Build ilspycmd NativeAOT` workflow builds the exact commit of a public
`icsharpcode/ILSpy` Release on Linux, macOS, and Windows runners. It publishes
five archives and SHA-256 checksums to an existing draft Release. macOS builds
are not notarized. No unit tests or CLI smoke checks run in this workflow.

To publish another version, check the upstream Release and create a matching
fork tag and draft before dispatching the workflow:

```sh
TAG=v11.2 # replace with the chosen upstream Release tag
gh release view "$TAG" --repo icsharpcode/ILSpy
git fetch https://github.com/icsharpcode/ILSpy.git "refs/tags/$TAG"
git tag "$TAG" FETCH_HEAD
git push origin "refs/tags/$TAG"
gh release create "$TAG" --repo TsingShui/ILSpy --verify-tag --draft \
  --title "ilspycmd $TAG" \
  --notes "Unofficial NativeAOT builds of icsharpcode/ILSpy $TAG. macOS binaries are not notarized."
gh workflow run myre-ilspycmd.yml --repo TsingShui/ILSpy -f upstream_tag="$TAG"
```

Do not move or replace an existing tag or published Release. The workflow
checks that both upstream and fork tags resolve to the built commit before
uploading. If the build succeeded but publishing failed, create or retain the
draft and dispatch `myre-ilspycmd-upload.yml` with the upstream tag and the
original build run ID. Then update the myre catalog's fixed tag and digests
from the published assets.

The workflow's .NET SDK channel follows the upstream v11.2 build configuration;
review `global.json` and upstream AOT settings before publishing another tag.
