# Building maya-usd against USD 26.x for Maya 2027

Maya 2027 ships USD 25.11 and upstream maya-usd supports up to that. This fork builds and
runs against **OpenUSD 26.08** (Python 3.13). What it took:

## USD

Build OpenUSD 26.08 with the defaults (imaging, usdview, MaterialX, Python) and **`--onetbb`**:

```bat
python build_scripts\build_usd.py --build-shared --usd-imaging --usdview --materialx --onetbb ^
    --build-variant release <usd-install>
```

Maya 2027 ships oneTBB (`tbb12.dll`). A USD built with classic TBB 2020 cannot be mixed with
Maya's headers in one translation unit (`tbb::split` is ambiguous, `tbb::task` is missing), and
two TBB runtimes would end up in one process.

## maya-usd

```bat
python build.py <workspace> --maya-location "C:\Program Files\Autodesk\Maya2027" ^
    --devkit-location <maya2027-devkit>\devkitBase --pxrusd-location <usd-install> ^
    --generator Ninja --build-release ^
    --build-args="-DBUILD_WITH_PYTHON_3=ON,-DBUILD_WITH_PYTHON_3_VERSION=3.13,-DPython_EXECUTABLE=<python 3.13 with Jinja2>,-DBUILD_AL_PLUGIN=OFF,-DMAYAUSD_SHADOW_MAYAHYDRA=ON"
```

- Build **Release**. USD's CMake config defines `TBB_USE_DEBUG` for RelWithDebInfo, which does not
  match USD's release libraries.
- `BUILD_AL_PLUGIN=OFF`: AL_USDMaya's picking code (`Engine.cpp`) uses `UsdImagingGLEngine`'s
  legacy `_taskController` / `_GetHdEngine`, which USD 26 replaced with the Hydra 2.0 scene-index
  task controller. It is not ported.
- `MAYAUSD_SHADOW_MAYAHYDRA=ON`: see below.
- USD 26 installs its Python modules in `Lib/site-packages` (25.x: `lib/python`); the build and the
  generated `mayaUSD.mod` handle both.

## Running it in Maya

Point `MAYA_MODULE_PATH` at the install directory. Autodesk's installed MayaUSD registers its
modules in `Common Files/Autodesk Shared/Modules/maya/2027/mayausd.mod`: `USD`, `MayaUSD_LIB`,
`MayaUSD` and `MAYAHYDRA`. Maya discards an installed module when `MAYA_MODULE_PATH` supplies one
of the same name, but not the ones it has no replacement for. Autodesk's MayaHydra (built against
Autodesk's USD) would stay active and put Autodesk's USD plugin path in front of USD 26, which
crashes Maya while loading plugins. `MAYAUSD_SHADOW_MAYAHYDRA=ON` installs an empty `MAYAHYDRA`
module so that does not happen. The cost is that MayaHydra is not available in that session.

Checked in Maya 2027 (`maya.exe -batch` and `mayapy`): `mayaUsdPlugin` loads from this install,
`pxr.Usd.GetVersion()` is `(0, 26, 8)`, and a stage with a prim can be created.
