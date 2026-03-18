# RapidFlex™ A1000 User Guide - Hot Plug Support

**Condensed excerpt from 189-page enterprise user guide**

---

## Document Overview

This excerpt is from the comprehensive **Western Digital RapidFlex™ A1000 User Guide**, a 189-page technical document covering the evaluation and use of RapidFlex™ A1000 bridge devices. The complete guide includes detailed information on architecture, command processing, management interfaces, electrical characteristics, and mechanical specifications.

**Full Documentation:** [View Complete PDF](rapidflex-a1000-user-guide-original.pdf) (189 pages)

**Document Number:** 1ET2193-A00  
**Product:** Western Digital RapidFlex™ A1000 Bridge Device  
**Audience:** System architects, hardware engineers, technical integrators

---

## 1.0 Introduction

### 1.1 Purpose

This user guide covers the evaluation and use of the Western Digital RapidFlex™ A1000 bridge devices. It is intended to provide the reader an overview of the device, an example of a typical use case, programming information, and electrical and mechanical information required for integration of this device into a customer's design.

In addition, this document serves as a reference for the functionality delivered in Western Digital's RapidFlex™ C1000 adapter board, based on the bridge device. NVMe-oF-specific information is also available within this user guide.

### 1.2 Identification

Devices covered by this document:

- **RapidFlex A1000**
  - Part Number: KN-103-xy
    - x = Major revision
    - y = Minor revision

### 1.3 Typographical Conventions

This document uses the following typographical conventions:

| Convention | Usage | Examples |
|------------|-------|----------|
| **Note:** | Important additional information or further clarification | Note: A weekly backup is recommended. |
| **Bold** | A command or system input that you type, or text or a button displayed on a screen. | Click **HELP** for details on disaster recovery. |
| *Italics* | Italic text indicates any of the following:<br>• *A term with a specific meaning* in the context of this document.<br>• *Emphasis* on specific information.<br>• *Reference* to another document. | Detailed information on *disaster recovery* methods is available in Administrator Guide. |
| Blue Text | References hyperlinks. | For more details, visit www.westerndigital.com. |

---

## 9.0 Hot Plug Support

The RapidFlex A1000 bridge supports hot plug of NVMe drives on its downstream ports. This section describes the various hot plug scenarios and the procedures for safely adding or removing drives.

### 9.1 Downstream Port Containment (DPC)

Downstream Port Containment (DPC) is a PCIe feature that provides error containment and recovery for hot plug events. The RapidFlex A1000 implements DPC to ensure safe hot plug operations without disrupting the host system.

**Key Features:**
- Automatic error detection and containment
- Prevention of error propagation to upstream devices
- Safe removal and insertion of drives during operation
- Recovery without system reboot

### 9.2 Hot Plug Scenarios

The following scenarios describe the recommended procedures for various hot plug operations.

#### 9.2.1 Adding a Drive to an Empty Slot

**Scenario:** Installing a new NVMe drive into an empty downstream port.

**Prerequisites:**
- Empty slot with no drive installed
- System powered on and operational
- No active I/O operations on the target slot

**Procedure:**

1. Verify the target slot is empty and ready for insertion
2. Align the NVMe drive with the M.2 connector
3. Insert the drive at a 30-degree angle into the M.2 slot
4. Press down gently until the drive is seated flush with the connector
5. Secure the drive using the appropriate retention mechanism (screw or clip)
6. The bridge will automatically detect the new drive
7. Wait for the system to enumerate and initialize the drive (typically 2-5 seconds)
8. Verify drive detection through management interface

**Expected Behavior:**
- Drive LED will illuminate when properly seated
- System will detect the drive automatically
- Drive will appear in the management interface
- No system interruption or error messages

**Troubleshooting:**
- If drive is not detected, verify physical connection
- Check drive compatibility with RapidFlex A1000
- Review system logs for any enumeration errors

---

#### 9.2.2 Removing a Drive from a Slot with No Host Connection

**Scenario:** Removing an NVMe drive that has no active host connections or I/O operations.

**Prerequisites:**
- Drive must not have active I/O operations
- No host connections to the drive
- Drive is in idle state

**Procedure:**

1. Verify no active I/O operations on the target drive
2. Check management interface to confirm drive is idle
3. Remove retention mechanism (unscrew or release clip)
4. Gently lift the drive at a 30-degree angle from the connector
5. Store the drive in an anti-static container

**Expected Behavior:**
- Drive removal is detected by the bridge
- Drive LED extinguishes
- No system errors or warnings
- Slot remains available for future insertion

**Caution:** Even with no host connection, always verify the drive is idle before removal to prevent potential data corruption on the drive itself.

---

#### 9.2.3 Removing a Drive from a Slot While Host Has Connections

**Scenario:** Removing a drive that currently has active host connections (not recommended during active I/O).

**Prerequisites:**
- Host has established connections to the drive
- **Critical:** No active I/O operations should be in progress

**Procedure:**

1. **STOP all I/O operations** to the target drive
2. Use the management interface to verify I/O has ceased
3. Disconnect host connections through proper shutdown procedures
4. Wait for all outstanding commands to complete (check command queue status)
5. Verify drive is in idle state
6. Remove retention mechanism
7. Carefully remove the drive at a 30-degree angle

**Expected Behavior:**
- Host connection termination is logged
- Outstanding commands are completed or aborted safely
- Drive removal is detected
- System continues normal operation with remaining drives

**Warning:** Removing a drive during active I/O can result in:
- Data loss or corruption
- Host system errors
- Incomplete write operations
- Potential file system damage

**Best Practice:** Always coordinate drive removal with the host system administrator and ensure proper shutdown procedures are followed.

---

#### 9.2.4 Removing a Drive from a Slot with Host Connected and Running FIO

**Scenario:** Emergency removal of a drive while the Flexible I/O (FIO) tester is running (not recommended).

**Prerequisites:**
- FIO operations are active on the drive
- Host connection is established
- **This is an emergency procedure only**

**Critical Warning:** This procedure should only be used in emergency situations. Normal operations should follow procedure 9.2.3.

**Emergency Procedure:**

1. **Acknowledge that data loss will occur**
2. If possible, stop FIO operations gracefully:
   - Send termination signal to FIO process
   - Wait for current operations to complete (if time permits)
3. If immediate removal is required:
   - Remove retention mechanism
   - Extract drive carefully
4. Expect the following consequences:
   - Active I/O operations will fail
   - Host system may log errors
   - Data in write buffers will be lost
   - File system may require recovery

**Post-Removal Actions:**

1. Check host system logs for errors
2. Verify remaining drives are operational
3. Run file system check (fsck) if the removed drive contained critical data
4. Document the incident for system maintenance records

**Recovery:**
- Host system should detect drive removal
- Outstanding I/O operations will timeout and fail
- Applications should implement error handling for failed operations
- System administrator should verify data integrity on remaining drives

**Note:** This scenario demonstrates the importance of proper I/O management and graceful shutdown procedures. Emergency removals should be avoided whenever possible.

---

#### 9.2.5 Drive Removal and Replacement with a New Drive

**Scenario:** Replacing a failed or underperforming drive with a new one.

**Prerequisites:**
- Replacement drive is compatible with RapidFlex A1000
- Replacement drive has same or greater capacity (recommended)
- Existing drive has been properly removed using procedures 9.2.2 or 9.2.3

**Procedure:**

1. Complete drive removal using appropriate procedure (9.2.2 or 9.2.3)
2. Wait 5 seconds for the bridge to fully reset the port
3. Verify the slot is empty and ready for new drive
4. Follow insertion procedure from section 9.2.1
5. Verify new drive detection and initialization
6. Configure new drive as needed (formatting, partitioning, etc.)
7. Resume normal operations

**Expected Behavior:**
- Old drive removal is detected and logged
- Port reset completes successfully
- New drive is detected and enumerated
- New drive appears in management interface with fresh configuration
- System operates normally with new drive

**Best Practices:**
- Document drive serial numbers for tracking
- Verify new drive firmware is up to date
- Run diagnostics on new drive before production use
- Update asset management database

---

### 9.3 Locate LED

The RapidFlex A1000 provides Locate LED functionality to help identify specific drives in multi-drive configurations.

#### 9.3.1 Set Opcode

Use the management interface to activate the Locate LED for a specific drive.

**Command Format:**
```
set_locate_led <port_number> <state>
```

**Parameters:**
- `port_number`: Downstream port number (0-N)
- `state`: LED state
  - `on`: Illuminate LED
  - `off`: Extinguish LED
  - `blink`: Blink LED at 1Hz

**Example:**
```
set_locate_led 2 blink
```

This command causes the LED for the drive on port 2 to blink, making it easy to physically locate.

---

#### 9.3.2 Locate LED VUC

The Locate LED can also be controlled via Vendor Unique Commands (VUC) through the NVMe management interface.

**VUC Command Structure:**

| Field | Value | Description |
|-------|-------|-------------|
| Opcode | 0xC0 | Vendor Unique Command |
| CDW10 | Port number | Target downstream port |
| CDW11 | LED state | 0=Off, 1=On, 2=Blink |

**Usage:**
Vendors can integrate Locate LED control into their management software using standard NVMe admin commands with the VUC opcode.

---

#### 9.3.3 Async Notification/Locate LED RLMP Object

The RapidFlex A1000 can send asynchronous notifications when the Locate LED state changes.

**Notification Format:**

| Field | Description |
|-------|-------------|
| Event Type | LOCATE_LED_CHANGE |
| Port Number | Affected downstream port |
| LED State | Current state (On/Off/Blink) |
| Timestamp | Event occurrence time |

**Use Cases:**
- Monitoring LED status changes from remote management
- Auditing LED control operations
- Integrating with facility management systems
- Troubleshooting physical drive identification issues

---

#### 9.3.4 Light Drive LED

Some NVMe drives include integrated LEDs that can be controlled independently of the bridge's Locate LED.

**Procedure:**

1. Determine if the installed drive supports integrated LED control
2. Use NVMe vendor-specific commands to control drive LED
3. Coordinate with bridge Locate LED for consistent indication

**Benefits:**
- Dual indication for critical drives
- Redundant visual identification
- Enhanced troubleshooting capability

**Note:** Not all NVMe drives support integrated LED control. Consult drive specifications for compatibility.

---

## Best Practices Summary

### Hot Plug Operations

1. **Always verify drive idle state** before removal
2. **Follow proper shutdown procedures** for drives with host connections
3. **Wait for enumeration to complete** after drive insertion
4. **Document all hot plug operations** for maintenance records
5. **Use Locate LED** to prevent removal errors in multi-drive systems

### Safety Considerations

- **Never remove a drive during active I/O** unless in an emergency situation
- **Use anti-static precautions** when handling drives
- **Verify drive compatibility** before insertion
- **Monitor system logs** after hot plug events
- **Implement timeout and error handling** in applications

### System Integration

- **Coordinate with system administrators** for planned removals
- **Implement monitoring** for drive addition/removal events
- **Use management interface** to verify operations
- **Test hot plug procedures** in non-production environments first
- **Train personnel** on proper hot plug procedures

---

## Additional Resources

For complete documentation including:
- Architecture and command processing
- Management interfaces (CLI, NVMe-MI, RLMP)
- Electrical characteristics and specifications
- Mechanical information and dimensions
- Firmware update procedures
- Network connectivity options
- PCIe switch support

**Refer to:** [Complete RapidFlex A1000 User Guide](rapidflex-a1000-user-guide-original.pdf)

---

## Skills Demonstrated in This Document

✅ **Enterprise Technical Writing** - Complex hardware documentation for professional audiences  
✅ **Procedural Documentation** - Step-by-step instructions with clear prerequisites and outcomes  
✅ **Risk Communication** - Appropriate warnings and cautions for critical operations  
✅ **Technical Accuracy** - Precise terminology and specifications  
✅ **User-Centered Design** - Scenario-based organization for real-world use cases  
✅ **Safety Documentation** - Clear communication of potential risks and mitigation strategies  
✅ **Comprehensive Coverage** - Multiple scenarios with troubleshooting and best practices  

---

*Excerpt from Western Digital RapidFlex™ A1000 User Guide (Document 1ET2193-A00)*  
*Original document: 189 pages | This excerpt: Hot Plug Support section*