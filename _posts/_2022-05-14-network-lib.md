My research on making a bluetooth lib<!--more-->

### Concept

**Umbrella lib**
- PeerLib (WiFi/BT peer library) 🚫 (makes things too complicated, and BT alone is fine)

**Bluetooth**
- BluePeer (Bluetooth peer library) (Similar API as MultiSync etc)
- BlueLib (Bluetooth library)

**WiFi**
- NetPeer (WiFi peer lib) 🚫
- NetLib (WiFi socket lib) 🚫

### BluePeer
- Response-time is not important (Sync happens lazily in the background when nearby)
- Speed is not important, account data doesn't need a lot of space
- Max 8 participants at the time (alleged iOS cap per app)
- CRDT stores meta data on each node regarding what data each has. (this enables one-way forward sync)
- New data is sent to nodes that are missing it. With a one-way forward sync. (mark on send)

### BluePeer sudo code:

```swift
BluePeer.shared.start() // Auto-connect with approved peers (advertise and scan)
BluePeer.shared.send(data, serviceID) // sends to all peers registered on this serviceID
BluePeer.shared.receive { data, peer in print(data.count) } // receive data callback
BluePeer.shared.stop() // stop advertising / scanning
// We need a way to avoid scanning if already added as a peripheral
// I guess we have to keep track of which peer is connected, and avoid connecting to peers that are already connected etc.
// what happens if two devices scan and try to connect to each other? I guess we can keep track of conecctingPeers and filter that array before attempting to connect.

```

### BlueLib

```
// BLNode (can be client or server)
// connected: [BLNode] // Current connected nodes (CBCentral, CBCentralManager)
// send (data, node)
// receive (callback)

// Deciding who gets server or client role:
// 1. Always advertise (also when app is standby)
// 2. BLNode.nodes.count < BLNode.connected { BLNode.scan { $0.reconnect() } }
// 3. A device (server or client) should be able to send and receive, regardless it's role
```

```swift
/// Helps distinguish one BlueLib instance from another.
public let uuid = UUID()
```

```swift
/**
 * Abstract: Transfer service and characteristics UUIDs
 */
import Foundation
import CoreBluetooth

struct TransferService {
	static let serviceUUID = CBUUID(string: "E20A39F4-73F5-4BC4-A12F-17D1AD07A961")
	static let characteristicUUID = CBUUID(string: "08590F7E-DB05-467E-8757-72F6FAEB13D4")
}
```

**BLPheripheral**
```swift
// You can generate UUIDs in the OSX terminal using the command "uuidgen".
let peripheral = BLPeripheral()
peripheral.delegate = self
do {
	let serviceUUID = NSUUID(UUIDString: "6E6B5C64-FAF7-40AE-9C21-D4933AF45B23")!
	let characteristicUUID = NSUUID(UUIDString: "477A2967-1FAB-4DC5-920A-DEE5DE685A3D")!
	let localName = "My Cool Peripheral"
	let configuration = BLPeripheralConfiguration(dataServiceUUID: serviceUUID, dataServiceCharacteristicUUID: 	characteristicUUID, localName: localName)
	try peripheral.startWithConfiguration(configuration)
	// You are now ready for incoming connections
} catch let error {
	// Handle error.
}
```

**BLCentral:**
```swift
let central = BLCentral()
central.delegate = self
central.addAvailabilityObserver(self)
do {
	let serviceUUID = NSUUID(UUIDString: "6E6B5C64-FAF7-40AE-9C21-D4933AF45B23")!
	let characteristicUUID = NSUUID(UUIDString: "477A2967-1FAB-4DC5-920A-DEE5DE685A3D")!
	let configuration = BLConfiguration(dataServiceUUID: serviceUUID, dataServiceCharacteristicUUID: characteristicUUID)
	try central.startWithConfiguration(configuration: configuration)
	// Once the availability observer has been positively notified, you're ready to discover and connect to peripherals.
} catch let error {
	// Handle error.
}
```
**Scan**
```swift
central.scanWithDuration(3, progressHandler: { newDiscoveries in
	// Handle newDiscoveries, [BKDiscovery].
}, completionHandler: { result, error in
	// Handle error.
	// If no error, handle result, [BKDiscovery].
})
```
**Connect:**
```swift
central.connect(remotePeripheral: peripherals[indexPath.row]) { remotePeripheral, error in
	// Handle error.
	// If no error, you're ready to receive data!
}
```

```swift
/// The available events a queue can and should respond to.
enum Event {
    case didDiscoverServices
    case didDiscoverCharacteristics
    case didDiscoverPeripheral(CBPeripheral, [String: Any], NSNumber)
    case didConnectPeripheral(Peripheral)
    case didDisconnectPeripheral(Peripheral)
    case didReadCharacteristic(CBCharacteristic, Data)
    case didWriteCharacteristic(CBCharacteristic)
    case didUpdateCharacteristicNotificationState(CBCharacteristic)
}
```

ScanModel:

```swift
/// A model capturing what is found from a scan callback.
public struct ScanDiscovery {
    /// The unique, persistent identifier associated with the peer.
    public let peripheralIdentifier: PeripheralIdentifier

    /// The advertisement packet the discovered peripheral is sending.
    public let advertisementPacket: [String: Any]

    /// The signal strength of the peripheral discovered.
    public let rssi: Int
}
```

### Central delegate state handling:

```swift
// implementations of the CBPeripheralManagerDelegate methods
/*
 *  Required protocol method.  A full app should take care of all the possible states,
 *  but we're just waiting for to know when the CBPeripheralManager is ready
 *
 *  Starting from iOS 13.0, if the state is CBManagerStateUnauthorized, you
 *  are also required to check for the authorization state of the peripheral to ensure that
 *  your app is allowed to use bluetooth
 */
internal func peripheralManagerDidUpdateState(_ peripheral: CBPeripheralManager) {

	 advertisingSwitch.isEnabled = peripheral.state == .poweredOn

	 switch peripheral.state {
	 case .poweredOn:
		  // ... so start working with the peripheral
		  os_log("CBManager is powered on")
		  setupPeripheral()
	 case .poweredOff:
		  os_log("CBManager is not powered on")
		  // In a real app, you'd deal with all the states accordingly
		  return
	 case .resetting:
		  os_log("CBManager is resetting")
		  // In a real app, you'd deal with all the states accordingly
		  return
	 case .unauthorized:
		  // In a real app, you'd deal with all the states accordingly
		  if #available(iOS 13.0, *) {
				switch peripheral.authorization {
				case .denied:
					 os_log("You are not authorized to use Bluetooth")
				case .restricted:
					 os_log("Bluetooth is restricted")
				default:
					 os_log("Unexpected authorization")
				}
		  } else {
				// Fallback on earlier versions
		  }
		  return
	 case .unknown:
		  os_log("CBManager state is unknown")
		  // In a real app, you'd deal with all the states accordingly
		  return
	 case .unsupported:
		  os_log("Bluetooth is not supported on this device")
		  // In a real app, you'd deal with all the states accordingly
		  return
	 @unknown default:
		  os_log("A previously unknown peripheral manager state occurred")
		  // In a real app, you'd deal with yet unknown cases that might occur in the future
		  return
	 }
}
```

**Clean up**

```swift
/*
 *  Call this when things either go wrong, or you're done with the connection.
 *  This cancels any subscriptions if there are any, or straight disconnects if not.
 *  (didUpdateNotificationStateForCharacteristic will cancel the connection if a subscription is involved)
 */
private func cleanup() {
	 // Don't do anything if we're not connected
	 guard let discoveredPeripheral = discoveredPeripheral,
		  case .connected = discoveredPeripheral.state else { return }

	 for service in (discoveredPeripheral.services ?? [] as [CBService]) {
		  for characteristic in (service.characteristics ?? [] as [CBCharacteristic]) {
			   if characteristic.uuid == TransferService.characteristicUUID && characteristic.isNotifying {
					 // It is notifying, so unsubscribe
					 self.discoveredPeripheral?.setNotifyValue(false, for: characteristic)
			   }
		  }
	 }

	 // If we've gotten this far, we're connected, but we're not subscribed, so we just disconnect
	 centralManager.cancelPeripheralConnection(discoveredPeripheral)
}
```

nice Errors: https://github.com/netguru/BlueSwift/blob/master/Framework/Source%20Files/Model/BluetoothError.swift

**A way to assert if characteristic sends response on write**:
We can also assert this on the peripheral
```swift
/// A bool indicating a peripheral can write the characteristic’s value with a response.
	public var isWriteWithResponse: Bool? {
		 return rawCharacteristic?.properties.contains(.write)
	}

```

**A way to send to a central**:
```swift
peripheralManager.updateValue(data, for: characteristicData, onSubscribedCentrals: [ remoteCentral.central ])
```
### NetPeer
- Use NWConnection and Bonjour see example here: https://developer.apple.com/documentation/network/building_a_custom_peer-to-peer_protocol
- Probably don't use Salut code (Figure out limitations of NW)
- Has Bonjour and Socket code: https://gitlab.com/theswiftdev/networking-for-appleos/-/tree/master/Shared/Sources
