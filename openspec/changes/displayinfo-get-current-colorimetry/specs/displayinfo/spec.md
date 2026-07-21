## ADDED Requirements

### Requirement: IDisplayProperties CurrentColorimetry method
The `Exchange::IDisplayProperties` interface SHALL include a `CurrentColorimetry(ColorimetryType& value)` virtual method annotated as a `@property` (read-only), which returns the single active colorimetry standard of the HDMI output link as a scalar `ColorimetryType` value.

#### Scenario: Interface method declared and bound
- **WHEN** `Exchange::JDisplayProperties::Register(*this, _displayProperties)` is called during plugin initialisation
- **THEN** the `DisplayInfo.1.getCurrentColorimetry` JSON-RPC property SHALL be registered and accessible to clients
