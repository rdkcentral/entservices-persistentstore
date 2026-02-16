# PersistentStore Plugin Architecture

## Overview

The PersistentStore plugin is a WPEFramework (Thunder) plugin that provides a persistent key-value storage service for RDK devices. It enables applications and services to store, retrieve, and manage configuration data and application state that persists across device reboots and power cycles. The plugin implements a secure, reliable, and efficient storage mechanism using SQLite as the underlying database engine.

## System Architecture

### Component Hierarchy

```
┌─────────────────────────────────────────────────────────┐
│              WPEFramework (Thunder)                      │
│                 Plugin Host                              │
└─────────────────────┬───────────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────────┐
│            PersistentStore Plugin                        │
│  ┌──────────────────────────────────────────────────┐   │
│  │          JSON-RPC Interface Layer                 │   │
│  │  (setValue, getValue, deleteKey, getKeys, etc.)  │   │
│  └───────────────────┬──────────────────────────────┘   │
│                      │                                   │
│  ┌───────────────────▼──────────────────────────────┐   │
│  │    PersistentStoreImplementation                  │   │
│  │  (IStore, IStore2, IStoreCache, IStoreInspector) │   │
│  └───────────────────┬──────────────────────────────┘   │
│                      │                                   │
│  ┌───────────────────▼──────────────────────────────┐   │
│  │         Store2 (SQLite Backend)                   │   │
│  │   - Transaction Management                        │   │
│  │   - Data Validation & Limits                      │   │
│  │   - Namespace Isolation                           │   │
│  │   - Integrity Checks & Backup                     │   │
│  └───────────────────┬──────────────────────────────┘   │
└────────────────────────┴──────────────────────────────────┘
                       │
         ┌─────────────▼──────────────┐
         │   SQLite Database File      │
         │ (/opt/secure/persistent/    │
         │    rdkservicestore)         │
         └─────────────────────────────┘
```

## Core Components

### 1. PersistentStore Plugin Class
- **Role**: Main plugin interface and lifecycle manager
- **Responsibilities**:
  - Plugin initialization and deinitialization
  - Configuration management (path, size limits, encryption key)
  - JSON-RPC API registration and event handling
  - Remote connection monitoring and management
  - Legacy data migration from previous storage locations

### 2. PersistentStoreImplementation
- **Role**: Business logic and interface implementation
- **Responsibilities**:
  - Implements IStore, IStore2, IStoreCache, IStoreInspector, and IStoreLimit interfaces
  - Manages notification subscriptions for value change events
  - Delegates storage operations to the SQLite backend
  - Provides cache management and data inspection capabilities
  - Enforces storage limits and quota management

### 3. Store2 (SQLite Backend)
- **Role**: Data persistence and database management
- **Responsibilities**:
  - SQLite database operations (CRUD)
  - Transaction management and atomic operations
  - Data integrity checks and corruption detection
  - Automatic backup and recovery mechanisms
  - Namespace-based data isolation and organization
  - Storage size and value limits enforcement
  - Optional IARM Bus integration for system events

## Data Flow

### Write Operation Flow
1. Client calls `setValue(scope, namespace, key, value)` via JSON-RPC
2. PersistentStore validates request and forwards to PersistentStoreImplementation
3. Implementation validates scope, namespace, and enforces limits
4. Store2 backend validates value size and storage capacity
5. SQLite transaction begins
6. Data written to database with namespace isolation
7. Transaction committed
8. Change notification dispatched to subscribed clients
9. Success response returned to caller

### Read Operation Flow
1. Client calls `getValue(scope, namespace, key)` via JSON-RPC
2. PersistentStore forwards request to implementation
3. Implementation queries Store2 backend
4. Store2 executes SELECT query with namespace filter
5. Result retrieved from SQLite database
6. Value returned through interface chain to caller

## Key Features & Technical Details

### Storage Scope Management
- **Device Scope**: Global storage accessible by all namespaces
- **Account Scope**: User-specific storage (extensible for multi-user scenarios)
- Namespace isolation prevents key collisions between applications

### Data Integrity & Reliability  
- Automatic database integrity checks on startup
- Corruption detection with automatic recovery from backup
- Atomic transactions ensure consistency
- Periodic backup mechanisms for data protection

### Resource Management
- Configurable size limits: `maxsize` (total), `maxvalue` (per-value), `limit` (per-namespace)
- Storage quota enforcement prevents resource exhaustion
- Efficient cache management for performance optimization

### Security Features
- Support for encryption key configuration
- Secure storage path: `/opt/secure/persistent/rdkservicestore`
- Namespace-based access isolation

### System Integration
- Optional IARM Bus integration for system management events
- Remote process communication via Thunder RPC
- Event notification system for real-time updates
- Legacy path migration support for backward compatibility

## Dependencies

- **WPEFramework (Thunder)**: Plugin framework and service infrastructure
- **SQLite3**: Database engine for persistent storage
- **IARMBus** (optional): Inter-process communication for system events
- **System Libraries**: C++11 standard library, POSIX APIs

## Configuration

The plugin is configured via Thunder's plugin configuration with the following parameters:
- `path`: Database file location
- `legacypath`: Previous storage location for migration
- `key`: Optional encryption key
- `maxsize`: Maximum total storage size (default: 1MB)
- `maxvalue`: Maximum single value size (default: 10KB)
- `limit`: Default namespace storage limit (default: 50KB)

## Performance Considerations

- In-process or out-of-process execution modes supported
- Efficient SQLite indexing for fast key lookups
- Transaction batching for bulk operations
- Minimal memory footprint through streaming operations
- Optimized for embedded device constraints
