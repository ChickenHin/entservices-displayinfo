## ADDED Requirements

### Requirement: getCurrentColorimetry property exposed
The `DisplayInfo` plugin SHALL expose a `getCurrentColorimetry` read-only JSON-RPC property that returns a single `colorimetry` string enum value representing the active colorimetry standard on the HDMI output link.

#### Scenario: Display connected with known matrix coefficient
- **WHEN** a display is connected and `vPort.getMatrixCoefficients()` returns a known `dsDisplayMatrixCoefficients_t` value
- **THEN** `DisplayInfo.getCurrentColorimetry` SHALL return the corresponding `ColorimetryType` string (e.g., `"COLORIMETRY_BT2020RGB_YCBCR"`)
- **THEN** the return code SHALL be `ERROR_NONE`

#### Scenario: No display connected
- **WHEN** no display is connected (`vPort.isDisplayConnected()` returns false)
- **THEN** `DisplayInfo.getCurrentColorimetry` SHALL return `"COLORIMETRY_UNKNOWN"`
- **THEN** the return code SHALL be `ERROR_NONE`

### Requirement: DS matrix coefficient to colorimetry mapping
The DeviceSettings backend SHALL map `dsDisplayMatrixCoefficients_t` values to `ColorimetryType` enum values as follows: `dsDISPLAY_MATRIXCOEFFICIENT_BT_709` → `COLORIMETRY_BT709`; `dsDISPLAY_MATRIXCOEFFICIENT_SMPTE_170M` → `COLORIMETRY_SMPTE170M`; `dsDISPLAY_MATRIXCOEFFICIENT_XvYCC_709` → `COLORIMETRY_XVYCC709`; `dsDISPLAY_MATRIXCOEFFICIENT_eXvYCC_601` → `COLORIMETRY_XVYCC601`; `dsDISPLAY_MATRIXCOEFFICIENT_BT_2020_NCL` → `COLORIMETRY_BT2020RGB_YCBCR`; `dsDISPLAY_MATRIXCOEFFICIENT_BT_2020_CL` → `COLORIMETRY_BT2020YCCBCBRC`; all other values → `COLORIMETRY_UNKNOWN`.

#### Scenario: BT.709 matrix coefficient active
- **WHEN** `vPort.getMatrixCoefficients()` returns `dsDISPLAY_MATRIXCOEFFICIENT_BT_709`
- **THEN** the property SHALL return `"COLORIMETRY_BT709"`

#### Scenario: BT.2020 NCL matrix coefficient active
- **WHEN** `vPort.getMatrixCoefficients()` returns `dsDISPLAY_MATRIXCOEFFICIENT_BT_2020_NCL`
- **THEN** the property SHALL return `"COLORIMETRY_BT2020RGB_YCBCR"`

#### Scenario: BT.2020 CL matrix coefficient active
- **WHEN** `vPort.getMatrixCoefficients()` returns `dsDISPLAY_MATRIXCOEFFICIENT_BT_2020_CL`
- **THEN** the property SHALL return `"COLORIMETRY_BT2020YCCBCBRC"`

#### Scenario: SMPTE 170M matrix coefficient active
- **WHEN** `vPort.getMatrixCoefficients()` returns `dsDISPLAY_MATRIXCOEFFICIENT_SMPTE_170M`
- **THEN** the property SHALL return `"COLORIMETRY_SMPTE170M"`

#### Scenario: xvYCC 709 matrix coefficient active
- **WHEN** `vPort.getMatrixCoefficients()` returns `dsDISPLAY_MATRIXCOEFFICIENT_XvYCC_709`
- **THEN** the property SHALL return `"COLORIMETRY_XVYCC709"`

#### Scenario: xvYCC 601 matrix coefficient active
- **WHEN** `vPort.getMatrixCoefficients()` returns `dsDISPLAY_MATRIXCOEFFICIENT_eXvYCC_601`
- **THEN** the property SHALL return `"COLORIMETRY_XVYCC601"`

#### Scenario: Unknown or unmapped matrix coefficient
- **WHEN** `vPort.getMatrixCoefficients()` returns a value with no defined mapping
- **THEN** the property SHALL return `"COLORIMETRY_UNKNOWN"`

### Requirement: DeviceSettings exception handling
The DeviceSettings backend `CurrentColorimetry()` SHALL wrap all DeviceSettings library calls in a typed `catch (const device::Exception&)` handler. On exception, the method SHALL set `value` to `COLORIMETRY_UNKNOWN` and return `ERROR_NONE`.

#### Scenario: DeviceSettings library throws exception
- **WHEN** a `device::Exception` is thrown during `getMatrixCoefficients()`
- **THEN** `CurrentColorimetry()` SHALL set `value` to `COLORIMETRY_UNKNOWN`
- **THEN** `CurrentColorimetry()` SHALL return `ERROR_NONE`

### Requirement: Unsupported platform backends return ERROR_UNAVAILABLE
The Linux/DRM and BCM/RPI backend implementations of `CurrentColorimetry()` SHALL return `ERROR_UNAVAILABLE` (stub only — no DS matrix coefficient API available on those platforms).

#### Scenario: Linux/DRM backend queried
- **WHEN** the plugin is running on a Linux/DRM platform and `DisplayInfo.getCurrentColorimetry` is called
- **THEN** the property SHALL return `ERROR_UNAVAILABLE`

#### Scenario: RPI backend queried
- **WHEN** the plugin is running on a BCM/RPI platform and `DisplayInfo.getCurrentColorimetry` is called
- **THEN** the property SHALL return `ERROR_UNAVAILABLE`
