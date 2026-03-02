# PersistentStore Plugin - Product Overview

## Product Description

The PersistentStore plugin is a production-grade persistent storage service for RDK (Reference Design Kit) devices and set-top boxes. It provides a robust, secure, and efficient key-value storage solution that enables applications, services, and system components to reliably store configuration data, user preferences, and application state across device reboots, software updates, and power cycles.

## Key Functionality

### Core Storage Operations
- **Set Value**: Store key-value pairs organized by namespace and scope
- **Get Value**: Retrieve stored values with namespace isolation
- **Delete Key**: Remove individual keys or entire namespaces
- **Get Keys**: List all keys within a namespace for enumeration
- **Get Namespaces**: Discover all namespaces in a storage scope
- **Get Storage Size**: Query storage consumption per namespace
- **Flush Cache**: Force immediate persistence of cached data

### Value Change Notifications
- Real-time event notifications when stored values are modified
- Subscription-based notification system for interested clients
- Scoped event delivery (device-level or account-level)

## Primary Use Cases

### 1. Application Configuration Management
Applications store and retrieve configuration settings that persist across sessions, enabling consistent behavior and user experience. Examples include video player preferences, audio settings, parental controls, and feature toggles.

### 2. User Preferences & Personalization
Store user-specific preferences such as language selection, display settings, accessibility options, and customized UI layouts. Supports multi-user scenarios with account-scoped storage.

### 3. Application State Persistence
Maintain application state including last-played content position, session data, bookmark information, and progress tracking. Enables seamless resume functionality after device restarts.

### 4. System Configuration Storage
System services utilize the store for device configuration, network settings, authentication tokens, service credentials, and operational parameters that must survive reboots.

### 5. Feature Flag & A/B Testing
Store feature flags, experimental settings, and A/B test configurations that can be dynamically updated and persisted across device lifecycles.

### 6. Analytics & Telemetry State
Track analytics data collection states, telemetry preferences, and diagnostic information that requires persistence for compliance and operational monitoring.

## API Capabilities

### JSON-RPC Interface
The plugin exposes a comprehensive JSON-RPC API accessible via Thunder framework:

**Storage Operations**:
- `setValue`: Store data with namespace and key
- `getValue`: Retrieve stored data
- `deleteKey`: Remove specific key
- `deleteNamespace`: Remove all keys in namespace
- `getKeys`: List all keys in namespace
- `getNamespaces`: List all namespaces
- `getStorageSize`: Query namespace storage usage

**Cache Management**:
- `flushCache`: Force write-through to persistent storage

**Event Subscriptions**:
- `onValueChanged`: Subscribe to value modification events

### Interface-Based Programming
C++ applications can directly use Thunder interfaces:
- `IStore`: Legacy storage interface
- `IStore2`: Enhanced storage with scope support
- `IStoreCache`: Cache management operations
- `IStoreInspector`: Storage inspection and debugging
- `IStoreLimit`: Quota and limit management

## Integration Benefits

### Developer Experience
- **Simple API**: Intuitive key-value paradigm with namespace organization
- **Type Agnostic**: Store any string-serializable data (JSON, XML, binary-encoded)
- **Multiple Access Methods**: JSON-RPC, native interfaces, or command-line tools
- **Rich Notification System**: React to storage changes in real-time

### Operational Excellence
- **Zero Data Loss**: Atomic transactions ensure consistency
- **Automatic Recovery**: Built-in corruption detection and backup restoration
- **Migration Support**: Seamless upgrade from legacy storage systems
- **Namespace Isolation**: Prevent conflicts between components

### Platform Integration
- **Thunder Native**: First-class WPEFramework plugin
- **IARM Compatible**: Optional integration with legacy IARM bus systems
- **Containerized Support**: Works in isolated environments
- **Cross-Platform**: Portable across RDK device variants

## Performance & Reliability

### Performance Characteristics
- **Low Latency**: Typical read operations < 5ms, write operations < 20ms
- **High Throughput**: Supports hundreds of operations per second
- **Efficient Storage**: SQLite compression and indexing optimize space usage
- **Minimal Footprint**: Small memory and CPU overhead suitable for embedded devices

### Reliability Features
- **Data Integrity**: ACID-compliant transactions via SQLite
- **Corruption Protection**: Automatic integrity checks and recovery
- **Backup & Restore**: Periodic backups prevent data loss
- **Graceful Degradation**: Continues operation during non-critical failures
- **Storage Limits**: Prevents resource exhaustion with configurable quotas

### Security Considerations
- **Encrypted Storage**: Supports SQLite encryption extensions
- **Secure Path**: Database stored in protected filesystem location
- **Namespace Sandboxing**: Logical isolation between applications
- **Access Control**: Thunder security framework integration

## Deployment Scenarios

### Production Devices
- Set-top boxes (STB) in cable, satellite, and IPTV deployments
- Smart TVs running RDK software stack
- Streaming media devices and hybrid broadcast platforms
- Residential gateways with video capabilities

### Development & Testing
- Emulator and simulator environments
- Continuous integration test automation
- QA validation and regression testing
- Developer workstations and debugging setups

## Compatibility & Standards

- **Thunder Framework**: Compatible with Thunder R4.4+ 
- **RDK Version**: Tested with RDK-B and RDK-V releases
- **Standards Compliance**: Follows RDK community coding standards
- **Interface Versioning**: API version 2.0.6 with backward compatibility
- **Platform Support**: Linux-based embedded systems (ARM, x86)

## Migration & Upgrade Path

The plugin supports seamless migration from legacy storage systems:
- Automatic detection and import from `legacypath` locations
- One-time migration preserves existing data
- No application changes required during platform upgrades
- Backward-compatible API ensures smooth transitions

## Monitoring & Diagnostics

- Storage size queries for capacity planning
- Namespace enumeration for debugging
- Cache flush operations for testing
- Integration with Thunder logging and diagnostics
- Support for development-time inspection tools
