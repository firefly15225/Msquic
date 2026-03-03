# QUIC binding 
| Return Type | Function Name | Description |
|---|---|---|
| `QUIC_STATUS` | `QuicBindingInitialize` | Creates and initializes a binding, including socket, lookup state, listener list, and stateless-operation tracking. |
| `void` | `QuicBindingUninitialize` | Cleans up and frees a binding, including socket teardown and tracked stateless contexts. |
| `void` | `QuicBindingTraceRundown` | Emits binding/listener rundown trace information for diagnostics. |
| `void` | `QuicBindingGetLocalAddress` | Gets the binding’s local UDP address. |
| `void` | `QuicBindingGetRemoteAddress` | Gets the binding’s remote UDP address (for connected bindings). |
| `BOOLEAN` | `QuicBindingGetQtipEnabled` | Returns whether QTIP is enabled for the socket/binding. |
| `BOOLEAN` | `QuicBindingHasListenerRegistered` | Checks whether any listener is currently registered on the binding. |
| `QUIC_STATUS` | `QuicBindingRegisterListener` | Registers a listener on the binding with IP/ALPN conflict checks and ordered insertion. |
| `QUIC_LISTENER*` | `QuicBindingGetListener` | Finds the best matching listener for an incoming connection (address + ALPN). |
| `void` | `QuicBindingUnregisterListener` | Removes a listener from the binding. |
| `void` | `QuicBindingAcceptConnection` | Routes a new server connection to a listener and records negotiated ALPN/TLS accept context. |
| `BOOLEAN` | `QuicBindingAddSourceConnectionID` | Adds a connection’s source/local CID into the binding lookup table. |
| `void` | `QuicBindingRemoveSourceConnectionID` | Removes one source/local CID from the binding lookup table. |
| `void` | `QuicBindingRemoveConnection` | Removes a connection’s lookup entries (remote hash and local CIDs) from the binding. |
| `void` | `QuicBindingMoveSourceConnectionIDs` | Moves a connection’s local CIDs from one binding lookup to another. |
| `void` | `QuicBindingOnConnectionHandshakeConfirmed` | Removes handshake-time remote-hash lookup state after handshake confirmation. |
| `QUIC_STATELESS_CONTEXT*` | `QuicBindingCreateStatelessOperation` | Creates/deduplicates/throttles tracked stateless operation context for a remote endpoint. |
| `BOOLEAN` | `QuicBindingQueueStatelessOperation` | Queues a stateless worker operation (VN/Retry/Stateless Reset). |
| `void` | `QuicBindingProcessStatelessOperation` | Builds and sends the actual stateless packet for VN, Retry, or Stateless Reset. |
| `void` | `QuicBindingReleaseStatelessOperation` | Marks stateless context complete, optionally returns datagram, and frees/releases refs when eligible. |
| `BOOLEAN` | `QuicBindingQueueStatelessReset` | Validates and queues stateless reset for an unattributed short-header packet. |
| `BOOLEAN` | `QuicBindingPreprocessPacket` | Performs early packet prep/validation and may trigger stateless VN response for unsupported versions. |
| `BOOLEAN` | `QuicBindingShouldRetryConnection` | Decides whether to send Retry based on token validity and handshake-memory pressure. |
| `QUIC_CONNECTION*` | `QuicBindingCreateConnection` | Creates (or resolves collision with) a new server connection and inserts it into binding lookup. |
| `BOOLEAN` | `QuicBindingDropBlockedSourcePorts` | Drops packets from blocked/unsafe UDP source ports. |
| `BOOLEAN` | `QuicBindingDeliverPackets` | Demultiplexes packet chains to existing/new connections or stateless handling paths. |
| `void` | `QuicBindingReceive` | Main UDP receive callback: preprocesses datagrams, groups by CID, delivers subchains, and returns drops. |
| `void` | `QuicBindingUnreachable` | Handles datapath unreachable callbacks and notifies matching connection. |
| `void` | `QuicBindingSend` | Sends prepared datagrams via binding socket and updates send counters. |
| `void` | `QuicBindingHandleDosModeStateChange` | Broadcasts DoS-mode state changes to all listeners on the binding. |
| `BOOLEAN` | `QuicRetryTokenDecrypt` | Decrypts and validates Retry/NEW_TOKEN token contents using stateless retry keys. |

# QUIC configuration
| Return Type | Function Name | Description |
|---|---|---|
| `QUIC_STATUS` | `MsQuicConfigurationOpen` | Creates a new configuration from a registration, validates/builds ALPN list, applies optional app settings, loads storage-backed defaults, and links it into the registration. |
| `void` | `MsQuicConfigurationClose` | Closes a configuration handle by releasing the handle reference (which may trigger cleanup). |
| `void` | `MsQuicConfigurationLoadCredentialComplete` | Completion callback for TLS credential loading; stores created security config and invokes async app callback when requested. |
| `QUIC_STATUS` | `MsQuicConfigurationLoadCredential` | Starts TLS credential/security-config creation for a configuration (sync or async) and manages lifetime refs around that operation. |
| `void` | `QuicConfigurationUninitialize` | Final destructor path: removes configuration from registration, frees security/storage/resources, releases registration rundown ref, and frees memory. |
| `void` | `QuicConfigurationAddRef` | Increments configuration reference count (and debug ref-type counter in debug builds). |
| `void` | `QuicConfigurationRelease` | Decrements configuration reference count and calls `QuicConfigurationUninitialize` when last ref is released. |
| `void` | `QuicConfigurationTraceRundown` | Emits trace rundown information for a configuration object. |
| `void` | `QuicConfigurationSettingsChanged` | Rebuilds effective configuration settings from global/silo/app-specific storage and logs the updated settings. |
| `QUIC_STATUS` | `QuicConfigurationParamGet` | Returns configuration parameters such as settings, version settings, or version-negotiation enable flag. |
| `QUIC_STATUS` | `QuicConfigurationParamSet` | Sets configuration parameters such as settings, version settings, ticket keys, and version-negotiation enable flag (with validation/state checks). |

# QUIC congestion control
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicCongestionControlInitialize` | Selects and initializes the congestion control algorithm implementation (`CUBIC` or `BBR`) based on settings, with fallback to CUBIC for unknown values. |
| `BOOLEAN` | `QuicCongestionControlCanSend` | Algorithm-dispatch wrapper that returns whether sending more data is currently allowed. |
| `void` | `QuicCongestionControlSetExemption` | Algorithm-dispatch wrapper that grants temporary packet-send exemptions from normal congestion checks. |
| `void` | `QuicCongestionControlReset` | Algorithm-dispatch wrapper that resets congestion controller state (partial or full reset). |
| `uint32_t` | `QuicCongestionControlGetSendAllowance` | Algorithm-dispatch wrapper that returns how many bytes can be sent immediately. |
| `void` | `QuicCongestionControlOnDataSent` | Algorithm-dispatch wrapper notified when retransmittable bytes are sent. |
| `BOOLEAN` | `QuicCongestionControlOnDataInvalidated` | Algorithm-dispatch wrapper for removing bytes-in-flight that are neither ACKed nor lost (e.g., invalidated). |
| `BOOLEAN` | `QuicCongestionControlOnDataAcknowledged` | Algorithm-dispatch wrapper notified on ACK events to update congestion state. |
| `void` | `QuicCongestionControlOnDataLost` | Algorithm-dispatch wrapper notified on loss events to apply congestion response. |
| `void` | `QuicCongestionControlOnEcn` | Algorithm-dispatch wrapper notified on ECN congestion signals (if the active algorithm implements ECN handling). |
| `BOOLEAN` | `QuicCongestionControlOnSpuriousCongestionEvent` | Algorithm-dispatch wrapper for handling spurious congestion/loss signals. |
| `uint8_t` | `QuicCongestionControlGetExemptions` | Algorithm-dispatch wrapper returning remaining exemption count. |
| `void` | `QuicCongestionControlLogOutFlowStatus` | Algorithm-dispatch wrapper that logs current outflow/congestion status. |
| `uint32_t` | `QuicCongestionControlGetBytesInFlightMax` | Algorithm-dispatch wrapper returning max bytes-in-flight observed/tracked by the algorithm. |
| `uint32_t` | `QuicCongestionControlGetCongestionWindow` | Algorithm-dispatch wrapper returning current congestion window size (bytes). |
| `BOOLEAN` | `QuicCongestionControlIsAppLimited` | Algorithm-dispatch wrapper returning whether the sender is currently app-limited. |
| `void` | `QuicCongestionControlSetAppLimited` | Algorithm-dispatch wrapper marking the sender as app-limited. |

# QUIC Connection
| Return Type | Function Name | Description |
|---|---|---|
| `BOOLEAN` | `QuicConnIsServer` | Returns whether the connection is in server role. |
| `BOOLEAN` | `QuicConnIsClient` | Returns whether the connection is in client role. |
| `BOOLEAN` | `QuicConnIsClosed` | Returns whether the connection is in a closed state. |
| `QUIC_CONNECTION*` | `QuicStreamSetGetConnection` | Gets owning connection from stream-set component. |
| `QUIC_CONNECTION*` | `QuicCryptoGetConnection` | Gets owning connection from crypto component. |
| `QUIC_CONNECTION*` | `QuicSendGetConnection` | Gets owning connection from send component. |
| `QUIC_CONNECTION*` | `QuicCongestionControlGetConnection` | Gets owning connection from congestion-control component. |
| `QUIC_CONNECTION*` | `QuicLossDetectionGetConnection` | Gets owning connection from loss-detection component. |
| `QUIC_CONNECTION*` | `QuicDatagramGetConnection` | Gets owning connection from datagram component. |
| `void` | `QuicConnLogOutFlowStats` | Logs outbound flow/control stats. |
| `void` | `QuicConnLogInFlowStats` | Logs inbound flow/control stats. |
| `void` | `QuicConnLogStatistics` | Logs aggregated connection statistics. |
| `BOOLEAN` | `QuicConnAddOutFlowBlockedReason` | Adds an outflow blocked reason flag and reports if state changed. |
| `BOOLEAN` | `QuicConnRemoveOutFlowBlockedReason` | Removes an outflow blocked reason flag and reports if state changed. |
| `QUIC_STATUS` | `QuicConnAlloc` | Allocates and initializes a new connection object. |
| `void` | `QuicConnFree` | Frees a connection object and owned resources. |
| `void` | `QuicConnCloseHandle` | Handles user handle-close semantics for connection. |
| `void` | `QuicConnOnShutdownComplete` | Finalizes shutdown completion and related indications/cleanup. |
| `void` | `QuicConnValidate` | Performs debug validation of connection invariants. |
| `void` | `QuicConnAddRef` | Increments connection reference count. |
| `void` | `QuicConnRelease` | Decrements connection reference count and frees on last ref. |
| `BOOLEAN` | `QuicConnRegister` | Registers connection with registration/worker state. |
| `void` | `QuicConnUnregister` | Unregisters connection from registration/worker state. |
| `void` | `QuicConnQueueTraceRundown` | Queues trace-rundown operation for the connection. |
| `QUIC_STATUS` | `QuicConnIndicateEvent` | Delivers an upcall/event indication to app callbacks. |
| `BOOLEAN` | `QuicConnDrainOperations` | Drains queued connection operations on worker execution path. |
| `void` | `QuicConnQueueOper` | Queues a normal-priority operation to the connection. |
| `void` | `QuicConnQueuePriorityOper` | Queues a priority operation to the connection. |
| `void` | `QuicConnQueueHighestPriorityOper` | Queues a highest-priority operation to the connection. |
| `QUIC_STATUS` | `QuicConnStart` | Starts the connection handshake/start logic. |
| `QUIC_CID_HASH_ENTRY*` | `QuicConnGenerateNewSourceCid` | Generates and optionally advertises a new local/source CID. |
| `void` | `QuicConnGenerateNewSourceCids` | Ensures required number of source CIDs are available/generated. |
| `BOOLEAN` | `QuicConnRetireCurrentDestCid` | Retires active destination CID and switches to another if possible. |
| `QUIC_CID_HASH_ENTRY*` | `QuicConnGetSourceCidFromSeq` | Looks up source CID by sequence number. |
| `QUIC_CID_HASH_ENTRY*` | `QuicConnGetSourceCidFromBuf` | Looks up source CID by CID bytes. |
| `QUIC_CID_LIST_ENTRY*` | `QuicConnGetDestCidFromSeq` | Looks up destination CID by sequence number. |
| `void` | `QuicConnUpdateRtt` | Updates RTT metrics using ACK/loss timing. |
| `void` | `QuicConnTimerSetEx` | Sets a connection timer using explicit `TimeNow`. |
| `void` | `QuicConnTimerSet` | Sets a connection timer. |
| `void` | `QuicConnTimerCancel` | Cancels a connection timer. |
| `void` | `QuicConnTimerExpired` | Handles timer expiration and queues processing work. |
| `uint64_t` | `QuicConnGetAckDelay` | Returns effective ACK delay value to advertise/use. |
| `void` | `QuicConnOnQuicVersionSet` | Applies per-version behavior/config after version selection. |
| `void` | `QuicConnOnLocalAddressChanged` | Handles local-address update effects for connection state. |
| `QUIC_STATUS` | `QuicConnGenerateLocalTransportParameters` | Builds local transport parameters to encode in TLS/QUIC handshake. |
| `void` | `QuicConnRestart` | Restarts connection state (used in version/retry flows). |
| `QUIC_STATUS` | `QuicConnProcessPeerTransportParameters` | Validates and applies peer transport parameters. |
| `QUIC_STATUS` | `QuicConnSetConfiguration` | Binds configuration (settings/credentials) to a connection. |
| `void` | `QuicConnCleanupServerResumptionState` | Cleans server resumption ticket/state resources. |
| `void` | `QuicConnDiscardDeferred0Rtt` | Discards deferred 0-RTT packets/data. |
| `void` | `QuicConnFlushDeferred` | Flushes deferred packets/data into normal receive path. |
| `void` | `QuicConnCloseLocally` | Initiates local close with reason flags/status. |
| `void` | `QuicConnTransportError` | Triggers close due to transport-protocol error. |
| `void` | `QuicConnFatalError` | Triggers fatal/internal close path. |
| `void` | `QuicConnSilentlyAbort` | Aborts connection without user-visible close signaling. |
| `void` | `QuicConnResetIdleTimeout` | Recomputes/resets idle timeout timer state. |
| `void` | `QuicConnQueueRecvPackets` | Queues received packet chain for connection processing. |
| `void` | `QuicConnQueueUnreachable` | Queues ICMP/UDP unreachable notification for processing. |
| `void` | `QuicConnQueueRouteCompletion` | Queues route-resolution completion notification. |
| `void` | `QuicConnUpdatePeerPacketTolerance` | Updates peer packet-tolerance behavior/settings. |
| `QUIC_STATUS` | `QuicConnParamSet` | Sets connection parameter(s). |
| `QUIC_STATUS` | `QuicConnParamGet` | Gets connection parameter(s). |
| `uint16_t` | `QuicConnGetMaxMtuForPath` | Returns max MTU allowed for a path. |
| `void` | `QuicMtuDiscoveryCheckSearchCompleteTimeout` | Checks/handles MTU discovery search timeout completion. |

# QUIC Connection Pool
| Return Type | Function Name | Description |
|---|---|---|
| `QUIC_STATUS` | `QuicConnPoolAllocUniqueRssProcInfo` | Allocates/records unique RSS processor tuple info for pool fan-out. |
| `QUIC_CONN_POOL_RSS_PROC_INFO*` | `QuicConnPoolGetRssProcForTuple` | Finds RSS processor info entry for a tuple/hash. |
| `const char*` | `QuicConnPoolAllocServerNameCopy` | Allocates and stores a copied server-name string for pool-created connections. |
| `QUIC_STATUS` | `QuicConnPoolGetStartingLocalAddress` | Selects/derives initial local address for pool connection attempts. |
| `QUIC_STATUS` | `QuicConnPoolGetInterfaceIndexForLocalAddress` | Resolves interface index for selected local address. |
| `void` | `QuicConnPoolQueueConnectionClose` | Queues close on a pooled connection (graceful or immediate path). |
| `QUIC_STATUS` | `QuicConnPoolTryCreateConnection` | Creates/configures/starts one connection as part of pool setup. |
| `QUIC_STATUS` | `MsQuicConnectionPoolCreate` | Public API that creates and starts a connection pool (multiple connections with pool options). |

# QUIC Crypto
| Return Type | Function Name | Description |
|---|---|---|
| `BOOLEAN` | `QuicCryptoHasPendingCryptoFrame` | Returns whether there is crypto data pending to send (new or recovery). |
| `void` | `QuicCryptoDumpSendState` | Debug helper to dump crypto send-state internals. |
| `void` | `QuicCryptoValidate` | Debug helper to validate crypto state invariants. |
| `QUIC_STATUS` | `QuicCryptoInitialize` | Initializes crypto send/recv tracking structures. |
| `void` | `QuicCryptoUninitialize` | Cleans up crypto state, buffers, TLS context, and temporary allocations. |
| `QUIC_STATUS` | `QuicCryptoInitializeTls` | Creates/initializes TLS context and binds transport parameters/callback state. |
| `void` | `QuicCryptoReset` | Resets crypto send progression for restart/retry scenarios. |
| `QUIC_STATUS` | `QuicCryptoOnVersionChange` | Re-derives initial keys/state after negotiated QUIC version changes. |
| `void` | `QuicCryptoHandshakeConfirmed` | Marks handshake-confirmed state and performs related key/binding actions. |
| `BOOLEAN` | `QuicCryptoDiscardKeys` | Discards specified packet-space keys (initial/handshake/1-RTT) if present. |
| `QUIC_ENCRYPT_LEVEL` | `QuicCryptoGetNextEncryptLevel` | Returns next encryption level that has pending crypto bytes to transmit. |
| `BOOLEAN` | `QuicCryptoWriteOneFrame` | Writes one CRYPTO frame chunk at a target encryption level into a packet. |
| `void` | `QuicCryptoWriteCryptoFrames` | Emits required CRYPTO frames for send/recovery windows. |
| `BOOLEAN` | `QuicCryptoWriteFrames` | Top-level CRYPTO frame writer used by packet builder. |
| `BOOLEAN` | `QuicCryptoOnLoss` | Handles lost CRYPTO frame metadata and schedules retransmission windows. |
| `void` | `QuicCryptoOnAck` | Handles ACKed CRYPTO frame metadata and advances unacked/sparse ranges. |
| `QUIC_STATUS` | `QuicCryptoProcessDataFrame` | Validates/inserts one received CRYPTO frame into receive buffer state. |
| `QUIC_STATUS` | `QuicCryptoProcessFrame` | Entry for processing an incoming CRYPTO frame by key type. |
| `BOOLEAN` | `QuicConnReceiveTP` | Internal helper to deliver decoded peer transport parameters into connection state. |
| `void` | `QuicCryptoProcessTlsCompletion` | Handles TLS-process completion flags/events (keys, handshake milestones, errors). |
| `void` | `QuicCryptoProcessDataComplete` | Finalizes one crypto-data processing pass and updates scheduling/flags. |
| `QUIC_STATUS` | `QuicCryptoProcessData` | Feeds buffered CRYPTO stream data into TLS and drives handshake progression. |
| `QUIC_STATUS` | `QuicCryptoProcessAppData` | Feeds app-provided crypto data (e.g., resumption material) to TLS path. |
| `void` | `QuicCryptoCustomCertValidationComplete` | Completes async custom certificate validation from app and resumes handshake flow. |
| `void` | `QuicCryptoCustomTicketValidationComplete` | Completes async custom ticket validation from app and resumes handshake flow. |
| `QUIC_STATUS` | `QuicCryptoGenerateNewKeys` | Generates next generation of 1-RTT keys for key update. |
| `void` | `QuicCryptoUpdateKeyPhase` | Rotates key generations and updates key phase/tracking state. |
| `size_t` | `QuicCryptoAddrSize` | Returns encoded size for IPv4/IPv6 address in careful-resume state. |
| `void` | `QuicCryptoEncodeAddr` | Encodes address family + address bytes into careful-resume ticket format. |
| `BOOLEAN` | `QuicCryptoDecodeAddr` | Decodes careful-resume encoded address back into `QUIC_ADDR`. |
| `size_t` | `QuicEncodedCRStateSize` | Calculates encoded careful-resume state size for a given address size/state. |
| `uint32_t` | `QuicCryptoGetEncodeCRStateSize` | Returns total encoded careful-resume state size for a state object. |
| `void` | `QuicCryptoEncodeCRState` | Encodes careful-resume state into ticket buffer. |
| `BOOLEAN` | `QuicCryptoDecodeCRState` | Decodes careful-resume state from ticket buffer. |
| `QUIC_STATUS` | `QuicCryptoEncodeServerTicket` | Encodes server-side resumption ticket payload (TP, ALPN, app data, CR state). |
| `QUIC_STATUS` | `QuicCryptoDecodeServerTicket` | Decodes server ticket payload and validates/extracts TP, ALPN, app data, CR state. |
| `QUIC_STATUS` | `QuicCryptoEncodeClientTicket` | Wraps ticket data for client-side resumption storage/use. |
| `QUIC_STATUS` | `QuicCryptoDecodeClientTicket` | Decodes client ticket and extracts server ticket, TP, and version fields. |
| `QUIC_STATUS` | `QuicCryptoReNegotiateAlpn` | Re-evaluates/negotiates ALPN against available list during resume/validation paths. |
| `uint32_t` | `QuicCryptoTlsGetCompleteTlsMessagesLength` | Returns contiguous complete TLS handshake-message bytes available in a buffer. |
| `QUIC_STATUS` | `QuicCryptoTlsReadInitial` | Parses initial TLS handshake bytes for pre-accept info extraction. |
| `QUIC_STATUS` | `QuicCryptoTlsReadClientRandom` | Extracts ClientHello random into TLS secrets structure. |

# QUIC Crypto TLS
| Return Type | Function Name | Description |
|---|---|---|
| `BOOLEAN` | `QuicTpIdIsReserved` | Checks if a transport-parameter ID is in QUIC’s reserved pattern (`31*N+27`). |
| `QUIC_STATUS` | `QuicCryptoTlsReadSniExtension` | Parses and validates TLS SNI extension; stores first host-name into new-conn info. |
| `QUIC_STATUS` | `QuicCryptoTlsReadAlpnExtension` | Parses and validates ALPN extension; stores client ALPN list in new-conn info. |
| `QUIC_STATUS` | `QuicCryptoTlsReadExtensions` | Parses ClientHello extension block, enforces duplicates rules, decodes QUIC TP extension. |
| `QUIC_STATUS` | `QuicCryptoTlsReadClientHello` | Parses ClientHello structure (version/random/session/ciphers/compression/extensions). |
| `uint32_t` | `QuicCryptoTlsGetCompleteTlsMessagesLength` | Scans TLS records/messages and returns length of complete handshake messages available. |
| `QUIC_STATUS` | `QuicCryptoTlsReadInitial` | Parses initial handshake flight, requiring ClientHello + ALPN (and optional SNI). |
| `QUIC_STATUS` | `QuicCryptoTlsReadClientRandom` | Extracts TLS ClientRandom from ClientHello bytes. |
| `const uint8_t*` | `QuicCryptoTlsEncodeTransportParameters` | Encodes QUIC transport parameters into TLS extension wire format buffer. |
| `BOOLEAN` | `QuicCryptoTlsDecodeTransportParameters` | Decodes/validates QUIC transport parameters from TLS extension into internal struct. |
| `QUIC_STATUS` | `QuicCryptoTlsCopyTransportParameters` | Deep-copies transport parameters (including version-info blob when present). |
| `void` | `QuicCryptoTlsCleanupTransportParameters` | Frees dynamic transport-parameter allocations (notably version-info) and clears flags. |

# QUIC cubic
| Return Type | Function Name | Description |
|---|---|---|
| `uint32_t` | `CubeRoot` | Computes integer cube root used by CUBIC math (`K` calculation). |
| `void` | `QuicConnLogCubic` | Logs key CUBIC state values (ssthresh, K, Wmax, last Wmax) for diagnostics. |
| `void` | `CubicCongestionHyStartChangeState` | Transitions HyStart++ state and updates related slow-start growth behavior. |
| `void` | `CubicCongestionHyStartResetPerRttRound` | Resets HyStart per-RTT-round sampling counters/min-RTT tracking. |
| `BOOLEAN` | `CubicCongestionControlCanSend` | Returns whether sending is allowed based on `BytesInFlight`, `CongestionWindow`, or exemptions. |
| `void` | `CubicCongestionControlSetExemption` | Sets number of packets allowed to bypass congestion window check. |
| `void` | `CubicCongestionControlReset` | Resets CUBIC state (window/recovery/HyStart), optionally full reset of bytes in flight. |
| `uint32_t` | `CubicCongestionControlGetSendAllowance` | Computes immediate send allowance, including pacing-based allowance when enabled. |
| `BOOLEAN` | `CubicCongestionControlUpdateBlockedState` | Updates connection blocked/unblocked state based on CUBIC send eligibility. |
| `void` | `CubicCongestionControlOnCongestionEvent` | Applies CUBIC response to congestion event (loss/ECN/persistent), updates windows and recovery state. |
| `void` | `CubicCongestionControlOnDataSent` | Accounts newly sent retransmittable bytes, updates in-flight/max, pacing allowance, exemptions. |
| `BOOLEAN` | `CubicCongestionControlOnDataInvalidated` | Removes invalidated bytes from in-flight and returns whether send became unblocked. |
| `void` | `CubicCongestionControlGetNetworkStatistics` | Fills network statistics snapshot from CUBIC/path/connection state. |
| `BOOLEAN` | `CubicCongestionControlOnDataAcknowledged` | Processes ACK feedback, handles recovery exit, HyStart, slow start/congestion avoidance growth. |
| `void` | `CubicCongestionControlOnDataLost` | Processes loss event, triggers congestion response when appropriate, updates in-flight. |
| `void` | `CubicCongestionControlOnEcn` | Processes ECN congestion signal similarly to loss-triggered congestion event handling. |
| `BOOLEAN` | `CubicCongestionControlOnSpuriousCongestionEvent` | Reverts CUBIC state when prior congestion event is detected as spurious. |
| `void` | `CubicCongestionControlLogOutFlowStatus` | Logs outbound flow status with CUBIC and path metrics. |
| `uint32_t` | `CubicCongestionControlGetBytesInFlightMax` | Returns peak observed bytes in flight. |
| `uint8_t` | `CubicCongestionControlGetExemptions` | Returns current exemption packet count. |
| `uint32_t` | `CubicCongestionControlGetCongestionWindow` | Returns current congestion window in bytes. |
| `BOOLEAN` | `CubicCongestionControlIsAppLimited` | Returns app-limited status for CUBIC (currently always `FALSE`). |
| `void` | `CubicCongestionControlSetAppLimited` | Placeholder hook for app-limited signaling (currently no-op for CUBIC). |
| `void` | `CubicCongestionControlInitialize` | Initializes CUBIC vtable/state and starting congestion window from settings/path MTU. |

# QUIC datagram
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicDatagramValidate` | Debug-only invariant checker for datagram queues, send flags, and max-length constraints. |
| `uint16_t` | `QuicCalculateDatagramLength` | Computes max datagram payload length from address family, MTU, CID overhead, and encryption overhead. |
| `void` | `QuicDatagramInitialize` | Initializes datagram send state, queue tails, lock, and default send limits. |
| `void` | `QuicDatagramIndicateSendStateChange` | Raises `QUIC_CONNECTION_EVENT_DATAGRAM_SEND_STATE_CHANGED` to app with updated client context. |
| `void` | `QuicDatagramCancelSend` | Cancels one queued datagram send request and indicates canceled state to app. |
| `void` | `QuicDatagramCompleteSend` | Completes one datagram send request, indicates sent state to app, and frees request. |
| `void` | `QuicDatagramUninitialize` | Uninitializes datagram component (asserts queues empty, destroys lock). |
| `void` | `QuicDatagramSendShutdown` | Disables datagram sending, clears send flags, and cancels all queued/api-pending datagram requests. |
| `void` | `QuicDatagramOnMaxSendLengthChanged` | Revalidates queued sends against new max length, canceling any oversized requests and updating send flags. |
| `void` | `QuicDatagramOnSendStateChanged` | Recomputes datagram send-enabled + max length from peer transport params/MTU and indicates state changes. |
| `QUIC_STATUS` | `QuicDatagramQueueSend` | Validates and queues app datagram send requests into API queue, then queues send operation. |
| `void` | `QuicDatagramSendFlush` | Moves API-queued datagrams to active send queue (priority-aware), canceling invalid/oversized ones. |
| `BOOLEAN` | `QuicDatagramWriteFrame` | Encodes queued datagram requests into DATAGRAM frames in packet builder, completing sends as written. |
| `BOOLEAN` | `QuicDatagramProcessFrame` | Decodes incoming DATAGRAM frame, indicates `DATAGRAM_RECEIVED` event, and updates recv counters. |
| `void` | `QuicDatagramCancelBlocked` | Cancels queued datagrams marked `CANCEL_ON_BLOCKED` and updates send-flag state accordingly. |

# QUIC frame
| Return Type | Function Name | Description |
|---|---|---|
| `BOOLEAN` | `QuicErrorIsProtocolError` | Returns whether a QUIC error code is in the transport protocol-error range used for stats. |
| `uint8_t*` | `QuicUint8Encode` | Encodes one byte and advances the output pointer. |
| `BOOLEAN` | `QuicUint8tDecode` | Decodes one byte at the current offset and advances offset. |
| `BOOLEAN` | `QuicAckHeaderEncode` | Encodes ACK frame header fields (largest acked, delay, block counts). |
| `BOOLEAN` | `QuicAckHeaderDecode` | Decodes ACK frame header fields and validates them. |
| `BOOLEAN` | `QuicAckBlockEncode` | Encodes one additional ACK block (gap + block length). |
| `BOOLEAN` | `QuicAckBlockDecode` | Decodes one additional ACK block. |
| `BOOLEAN` | `QuicAckEcnEncode` | Encodes ACK ECN counters. |
| `BOOLEAN` | `QuicAckEcnDecode` | Decodes ACK ECN counters. |
| `BOOLEAN` | `QuicAckFrameEncode` | Encodes a full ACK/ACK_ECN frame from range data. |
| `BOOLEAN` | `QuicAckFrameDecode` | Decodes ACK/ACK_ECN into ack ranges, ack delay, and optional ECN counters. |
| `BOOLEAN` | `QuicResetStreamFrameEncode` | Encodes `RESET_STREAM` frame. |
| `BOOLEAN` | `QuicResetStreamFrameDecode` | Decodes `RESET_STREAM` frame. |
| `BOOLEAN` | `QuicReliableResetFrameEncode` | Encodes `RELIABLE_RESET_STREAM` frame. |
| `BOOLEAN` | `QuicReliableResetFrameDecode` | Decodes `RELIABLE_RESET_STREAM` frame. |
| `BOOLEAN` | `QuicStopSendingFrameEncode` | Encodes `STOP_SENDING` frame. |
| `BOOLEAN` | `QuicStopSendingFrameDecode` | Decodes `STOP_SENDING` frame. |
| `BOOLEAN` | `QuicCryptoFrameEncode` | Encodes `CRYPTO` frame header and payload reference. |
| `BOOLEAN` | `QuicCryptoFrameDecode` | Decodes `CRYPTO` frame fields and payload span. |
| `BOOLEAN` | `QuicNewTokenFrameEncode` | Encodes `NEW_TOKEN` frame. |
| `BOOLEAN` | `QuicNewTokenFrameDecode` | Decodes `NEW_TOKEN` frame. |
| `uint8_t` | `QuicStreamFrameHeaderSize` | Computes encoded STREAM frame header size from flags/fields. |
| `BOOLEAN` | `QuicStreamFrameEncode` | Encodes STREAM frame header and payload metadata. |
| `BOOLEAN` | `QuicStreamFrameDecode` | Decodes STREAM frame fields and payload span. |
| `BOOLEAN` | `QuicMaxDataFrameEncode` | Encodes `MAX_DATA` frame. |
| `BOOLEAN` | `QuicMaxDataFrameDecode` | Decodes `MAX_DATA` frame. |
| `BOOLEAN` | `QuicMaxStreamDataFrameEncode` | Encodes `MAX_STREAM_DATA` frame. |
| `BOOLEAN` | `QuicMaxStreamDataFrameDecode` | Decodes `MAX_STREAM_DATA` frame. |
| `BOOLEAN` | `QuicMaxStreamsFrameEncode` | Encodes `MAX_STREAMS` frame. |
| `BOOLEAN` | `QuicMaxStreamsFrameDecode` | Decodes `MAX_STREAMS` frame. |
| `BOOLEAN` | `QuicDataBlockedFrameEncode` | Encodes `DATA_BLOCKED` frame. |
| `BOOLEAN` | `QuicDataBlockedFrameDecode` | Decodes `DATA_BLOCKED` frame. |
| `BOOLEAN` | `QuicStreamDataBlockedFrameEncode` | Encodes `STREAM_DATA_BLOCKED` frame. |
| `BOOLEAN` | `QuicStreamDataBlockedFrameDecode` | Decodes `STREAM_DATA_BLOCKED` frame. |
| `BOOLEAN` | `QuicStreamsBlockedFrameEncode` | Encodes `STREAMS_BLOCKED` frame. |
| `BOOLEAN` | `QuicStreamsBlockedFrameDecode` | Decodes `STREAMS_BLOCKED` frame. |
| `BOOLEAN` | `QuicNewConnectionIDFrameEncode` | Encodes `NEW_CONNECTION_ID` frame. |
| `BOOLEAN` | `QuicNewConnectionIDFrameDecode` | Decodes `NEW_CONNECTION_ID` frame. |
| `BOOLEAN` | `QuicRetireConnectionIDFrameEncode` | Encodes `RETIRE_CONNECTION_ID` frame. |
| `BOOLEAN` | `QuicRetireConnectionIDFrameDecode` | Decodes `RETIRE_CONNECTION_ID` frame. |
| `BOOLEAN` | `QuicPathChallengeFrameEncode` | Encodes `PATH_CHALLENGE` or `PATH_RESPONSE` frame. |
| `BOOLEAN` | `QuicPathChallengeFrameDecode` | Decodes `PATH_CHALLENGE`/`PATH_RESPONSE` payload. |
| `BOOLEAN` | `QuicConnCloseFrameEncode` | Encodes transport/application `CONNECTION_CLOSE` frame. |
| `BOOLEAN` | `QuicConnCloseFrameDecode` | Decodes `CONNECTION_CLOSE` frame. |
| `BOOLEAN` | `QuicDatagramFrameEncodeEx` | Encodes DATAGRAM frame from one or more buffers. |
| `BOOLEAN` | `QuicDatagramFrameDecode` | Decodes DATAGRAM frame (explicit or implicit length). |
| `BOOLEAN` | `QuicAckFrequencyFrameEncode` | Encodes `ACK_FREQUENCY` frame. |
| `BOOLEAN` | `QuicAckFrequencyFrameDecode` | Decodes `ACK_FREQUENCY` frame. |
| `BOOLEAN` | `QuicTimestampFrameEncode` | Encodes `TIMESTAMP` frame. |
| `BOOLEAN` | `QuicTimestampFrameDecode` | Decodes `TIMESTAMP` frame. |
| `BOOLEAN` | `QuicStreamFramePeekID` | Reads Stream ID from any stream-related frame without full decode. |
| `BOOLEAN` | `QuicStreamFrameSkip` | Decodes-and-skips a stream-related frame to advance packet offset. |
| `BOOLEAN` | `QuicFrameLog` | Parses and logs one frame from a packet for diagnostics/tracing. |
| `void` | `QuicFrameLogAll` | Iterates through a decrypted packet and logs all contained frames. |

# QUIC injection
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicFuzzInjectHook` | Fuzzer-only hook that signals emulator/fuzzer and allows callback-based inspection/modification of pre-encryption packet payload before send. |

# QUIC library
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `MsQuicLibraryLoad` | Performs process/global library load initialization (locks, lists, version info, trace callback). |
| `void` | `MsQuicLibraryUnload` | Performs final process/global unload cleanup when load refcount reaches zero. |
| `void` | `MsQuicCalculatePartitionMask` | Computes partition bitmask from partition count for partition-ID encoding/decoding. |
| `void` | `MsQuicLibraryFreePartitions` | Uninitializes and frees all library partitions. |
| `QUIC_STATUS` | `MsQuicLibraryInitialize` | Performs full lazy runtime initialization (platform, settings, storage, workers, defaults). |
| `void` | `MsQuicLibraryLazyUninitialize` | Tears down lazy-initialized runtime state. |
| `void` | `MsQuicLibraryUninitialize` | Final library uninitialization path for open-instance teardown. |
| `void` | `MsQuicLibraryOnSettingsChanged` | Applies new global settings and propagates relevant updates to registrations/state. |
| `void` | `MsQuicLibraryReadSettings` | Reloads settings from defaults/storage and triggers settings-change handling. |
| `QUIC_STATUS` | `MsQuicAddRef` | Public API-style open ref increment with lazy init checks. |
| `void` | `MsQuicRelease` | Public API-style open ref release and lazy cleanup trigger. |
| `QUIC_STATUS` | `MsQuicOpenVersion` | Opens and returns API table for requested MsQuic API version. |
| `void` | `MsQuicClose` | Closes MsQuic API table handle. |
| `void` | `MsQuicSetContext` | Sets client context pointer on a generic MsQuic handle. |
| `void*` | `MsQuicGetContext` | Gets client context pointer from a generic MsQuic handle. |
| `void` | `MsQuicSetCallbackHandler` | Sets callback handler and callback context for handle types that support it. |
| `QUIC_STATUS` | `MsQuicExecutionCreate` | Creates custom execution contexts / worker pool configuration. |
| `void` | `MsQuicExecutionDelete` | Deletes custom execution contexts / worker pool configuration. |
| `uint32_t` | `MsQuicExecutionPoll` | Polls one execution context for pending work. |
| `CXPLAT_DATAPATH_FEATURES` | `QuicLibraryGetDatapathFeatures` | Returns currently supported datapath capabilities based on active settings. |
| `QUIC_STATUS` | `QuicLibraryInitializePartitions` | Initializes per-partition state, hashing, retry-key state, and partition topology. |
| `void` | `QuicLibrarySumPerfCounters` | Aggregates perf counters across all partitions into one buffer. |
| `void` | `QuicLibrarySumPerfCountersExternal` | Thread-safe external wrapper to aggregate perf counters when library is open. |
| `void` | `QuicPerfCounterSnapShot` | Captures and logs sampled perf counters and checks runtime counter heuristics. |
| `void` | `QuicPerfCounterTrySnapShot` | Attempts periodic perf snapshot based on sample interval and CAS guard. |
| `void` | `QuicLibraryLoadRetryConfig` | Loads stateless retry key config from storage and applies it if valid. |
| `void` | `QuicLibApplyLoadBalancingSetting` | Applies connection-ID/load-balancing global settings before library is in-use. |
| `QUIC_STATUS` | `QuicLibrarySetGlobalParam` | Sets a global library parameter. |
| `QUIC_STATUS` | `QuicLibraryGetGlobalParam` | Gets a global library parameter. |
| `QUIC_STATUS` | `QuicLibrarySetParam` | Routes generic `SetParam` to the correct object/global handler by handle type. |
| `QUIC_STATUS` | `QuicLibraryGetParam` | Routes generic `GetParam` to the correct object/global handler by handle type. |
| `QUIC_STATUS` | `QuicLibraryLazyInitialize` | Ensures one-time lazy initialization is completed before normal operations. |
| `QUIC_BINDING*` | `QuicLibraryLookupBinding` | Finds an existing binding matching requested UDP configuration constraints. |
| `QUIC_STATUS` | `QuicLibraryGetBinding` | Gets or creates appropriate binding with sharing/collision logic and refcounting. |
| `BOOLEAN` | `QuicLibraryTryAddRefBinding` | Attempts to add binding ref only if binding is not already in cleanup. |
| `void` | `QuicLibraryReleaseBinding` | Releases a binding ref and uninitializes binding on last reference. |
| `BOOLEAN` | `QuicLibraryOnListenerRegistered` | Ensures shared stateless server registration exists when first listener registers. |
| `QUIC_WORKER*` | `QuicLibraryGetWorker` | Selects stateless worker for incoming packet partition. |
| `void` | `QuicTraceRundown` | Emits global rundown tracing for library, registrations, bindings, and counters. |
| `void` | `QuicLibraryOnHandshakeConnectionAdded` | Increments tracked handshake-memory usage and reevaluates retry mode. |
| `void` | `QuicLibraryOnHandshakeConnectionRemoved` | Decrements tracked handshake-memory usage and reevaluates retry mode. |
| `void` | `QuicLibraryEvaluateSendRetryState` | Recomputes `SendRetryEnabled` based on memory pressure and notifies bindings/listeners. |
| `QUIC_STATUS` | `QuicLibraryGenerateStatelessResetToken` | Generates stateless reset token from CID using partition hash context. |
| `QUIC_STATUS` | `QuicLibrarySetRetryKeyConfig` | Validates and updates stateless retry secret/algorithm/rotation configuration. |
| `QUIC_PARTITION*` | `QuicLibraryGetPartitionFromProcessorIndex` | Maps processor index to the partition used for scheduling and hashing. |
| `QUIC_PARTITION*` | `QuicLibraryGetCurrentPartition` | Returns partition associated with current processor. |
| `uint16_t` | `QuicPartitionIdCreate` | Creates partition ID with random high bits and encoded low-bit partition index. |
| `uint16_t` | `QuicPartitionIdGetIndex` | Extracts canonical partition index from encoded partition ID. |
| `QUIC_CID_HASH_ENTRY*` | `QuicCidNewRandomSource` | Allocates and builds a new random source CID (with server/prefix/partition fields). |
| `void` | `QuicLibraryInitializeDbg` | Initializes debug object tracking structures. |
| `void` | `QuicLibraryUninitializeDbg` | Validates and tears down debug object tracking structures. |
| `void` | `QuicLibraryTrackDbgObject` | Adds an object to debug lifetime tracking list/counter. |
| `void` | `QuicLibraryUntrackDbgObject` | Removes an object from debug lifetime tracking list/counter. |

# QUIC listener
| Return Type | Function Name | Description |
|---|---|---|
| `BOOLEAN` | `QuicListenerIsOnWorker` | Checks whether current execution context is on listener’s assigned worker/partition. |
| `QUIC_STATUS` | `MsQuicListenerOpen` | Creates a listener object, initializes state, and links it to the registration. |
| `void` | `MsQuicListenerClose` | Closes listener handle, stops listener if needed, waits for stop completion, and releases final ref. |
| `QUIC_STATUS` | `MsQuicListenerStart` | Validates ALPN/address config, gets binding, registers listener, and starts accepting connections. |
| `void` | `MsQuicListenerStop` | Public API to asynchronously stop a running listener. |
| `void` | `QuicListenerFree` | Final listener destructor: unlinks from registration, releases resources, and frees memory. |
| `QUIC_STATUS` | `QuicListenerIndicateEvent` | Delivers passive-level listener callback event to app handler. |
| `QUIC_STATUS` | `QuicListenerIndicateDispatchEvent` | Delivers dispatch-level listener event (DoS mode change) to app handler. |
| `void` | `QuicListenerEndStopComplete` | Marks listener fully stopped and signals stop event. |
| `void` | `QuicListenerIndicateStopComplete` | Indicates `STOP_COMPLETE` event to app with safe reference handling. |
| `void` | `QuicListenerBeginStopComplete` | Begins stop-complete workflow, optionally queues/indicates final stop callback. |
| `void` | `QuicListenerStartReference` | Increments active start reference count (blocks stop completion). |
| `void` | `QuicListenerStartRelease` | Decrements start reference; triggers stop-complete path on last ref. |
| `void` | `QuicListenerReference` | Increments internal listener lifetime reference count. |
| `void` | `QuicListenerRelease` | Decrements internal lifetime ref and frees listener on last ref. |
| `void` | `QuicListenerStopAsync` | Internal async stop: unregisters from binding and releases binding/start refs. |
| `void` | `QuicListenerTraceRundown` | Emits tracing rundown for listener state/configuration. |
| `const uint8_t*` | `QuicListenerFindAlpnInList` | Finds first matching ALPN honoring listener/server preference order. |
| `BOOLEAN` | `QuicListenerHasAlpnOverlap` | Returns whether two listeners have overlapping ALPNs. |
| `BOOLEAN` | `QuicListenerMatchesAlpn` | Matches client ALPN list against listener ALPNs and sets negotiated ALPN fields. |
| `BOOLEAN` | `QuicListenerClaimConnection` | Binds accepted connection to listener/app ownership and raises new-connection callback. |
| `void` | `QuicListenerAcceptConnection` | Registration-level accept pipeline for new connections (register, CID setup, app accept/reject accounting). |
| `QUIC_STATUS` | `QuicListenerParamSet` | Sets listener parameters (e.g., CIBIR ID, DoS events, partition index). |
| `QUIC_STATUS` | `QuicListenerParamGet` | Gets listener parameters and stats (local address, counters, CIBIR, DoS, partition). |
| `void` | `QuicListenerHandleDosModeStateChange` | Handles DoS mode changes; indicates immediately or queues worker delivery for partitioned listeners. |
| `BOOLEAN` | `QuicListenerDrainOperations` | Drains queued listener worker operations (DoS event and stop-complete event). |

# QUIC lookup
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicLookupInitialize` | Initializes lookup state and lock for connection-ID/remote-hash lookups. |
| `void` | `QuicLookupUninitialize` | Uninitializes lookup tables/locks and frees lookup resources. |
| `BOOLEAN` | `QuicLookupCreateHashTable` | Allocates and initializes partitioned hash tables for CID lookups. |
| `BOOLEAN` | `QuicLookupRebalance` | Rebalances lookup representation (single vs hash/partitioned hash) based on current mode/state. |
| `BOOLEAN` | `QuicLookupMaximizePartitioning` | Enables max partitioning mode and initializes remote-hash table support. |
| `BOOLEAN` | `QuicCidMatchConnection` | Checks whether an incoming destination CID matches any source CID on a connection. |
| `QUIC_CONNECTION*` | `QuicHashLookupConnection` | Finds connection in a hash table by CID hash and exact CID bytes. |
| `QUIC_CONNECTION*` | `QuicLookupFindConnectionByLocalCidInternal` | Internal local-CID lookup path (single-connection mode or partitioned hash mode). |
| `QUIC_CONNECTION*` | `QuicLookupFindConnectionByRemoteHashInternal` | Internal remote tuple/CID hash lookup in remote-hash table. |
| `BOOLEAN` | `QuicLookupInsertLocalCid` | Internal insert of local/source CID into lookup tables, with optional refcount update. |
| `BOOLEAN` | `QuicLookupInsertRemoteHash` | Internal insert of remote-hash entry for handshake/server routing, with optional refcount update. |
| `void` | `QuicLookupRemoveLocalCidInt` | Internal removal of one local/source CID from lookup tables and lookup bookkeeping. |
| `QUIC_CONNECTION*` | `QuicLookupFindConnectionByLocalCid` | Public local-CID lookup; returns referenced connection if found. |
| `QUIC_CONNECTION*` | `QuicLookupFindConnectionByRemoteHash` | Public remote hash lookup; returns referenced connection if found and enabled. |
| `QUIC_CONNECTION*` | `QuicLookupFindConnectionByRemoteAddr` | Finds connection by remote address (single-connection mode path). |
| `BOOLEAN` | `QuicLookupAddLocalCid` | Adds a source CID to lookup, optionally returning colliding connection. |
| `BOOLEAN` | `QuicLookupAddRemoteHash` | Adds remote hash entry for a connection, optionally returning collision. |
| `void` | `QuicLookupRemoveLocalCid` | Removes one local/source CID and releases corresponding lookup-table reference. |
| `void` | `QuicLookupRemoveRemoteHash` | Removes remote-hash entry and releases associated lookup-table reference. |
| `void` | `QuicLookupRemoveLocalCids` | Removes and frees all source CIDs owned by a connection from lookup. |
| `void` | `QuicLookupMoveLocalConnectionIDs` | Moves all in-lookup source CIDs from one lookup object to another. |

# QUIC loss detection
| Return Type | Function Name | Description |
|---|---|---|
| `BOOLEAN` | `QuicLossDetectionRetransmitFrames` | Retransmits/queues retransmission for retransmittable frames from a sent packet metadata entry. |
| `void` | `QuicLossDetectionOnPacketDiscarded` | Handles final cleanup/accounting when a sent/lost packet metadata entry is discarded. |
| `void` | `QuicLossDetectionInitializeInternalState` | Initializes core loss-detection counters/timestamps/probe state. |
| `void` | `QuicLossValidate` | Debug-only invariant checks for sent/lost packet lists and in-flight counters. |
| `void` | `QuicLossDetectionInitialize` | Initializes loss-detection lists and internal state. |
| `void` | `QuicLossDetectionUninitialize` | Cleans up all outstanding/lost tracked packets during shutdown. |
| `void` | `QuicLossDetectionReset` | Resets loss state/timers and retransmit-disposes tracked packets for restart paths. |
| `QUIC_SENT_PACKET_METADATA*` | `QuicLossDetectionOldestOutstandingPacket` | Returns oldest outstanding retransmittable packet metadata. |
| `uint64_t` | `QuicLossDetectionComputeProbeTimeout` | Computes PTO duration from RTT variance and peer max ACK delay. |
| `void` | `QuicLossDetectionUpdateTimer` | Arms/cancels/executes loss timer (RACK, initial, or probe timeout logic). |
| `void` | `QuicLossDetectionOnPacketSent` | Copies and tracks sent packet metadata, updates in-flight/counters/congestion-control signals. |
| `void` | `QuicLossDetectionOnPacketAcknowledged` | Processes one acknowledged packet’s frame effects and per-packet accounting updates. |
| `void` | `QuicLossDetectionDetectAndHandleLostPackets` | Detects losses using reordering/time thresholds and applies congestion/retransmission handling. |
| `void` | `QuicLossDetectionDiscardPackets` | Discards all tracked packets for a discarded key space. |
| `void` | `QuicLossDetectionOnZeroRttRejected` | Handles 0-RTT rejection by discarding/recovering affected packet tracking state. |
| `void` | `QuicLossDetectionProcessAckBlocks` | Applies decoded ACK ranges to sent/lost lists, updates RTT/ECN/congestion signals. |
| `BOOLEAN` | `QuicLossDetectionProcessAckFrame` | Decodes and validates ACK frame, then processes its ACK blocks. |
| `void` | `QuicLossDetectionScheduleProbe` | Schedules probe transmissions (typically 2 packets) when loss can’t yet be inferred. |
| `void` | `QuicLossDetectionProcessTimerOperation` | Handles loss timer firing: timeout close path or loss/probe actions plus timer re-arm. |

# QUIC mtu discovery
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicMtuDiscoverySendProbePacket` | Internal helper that sets send flag to emit an MTU probe packet. |
| `void` | `QuicMtuDiscoveryMoveToSearchComplete` | Internal helper that marks MTU discovery as search-complete and records entry time. |
| `uint16_t` | `QuicGetNextProbeSize` | Internal helper that computes next probe MTU (monotonic growth, includes 1500-byte special case). |
| `void` | `QuicMtuDiscoveryMoveToSearching` | Transitions path MTU discovery into searching mode and schedules next probe size/send. |
| `void` | `QuicMtuDiscoveryPeerValidated` | Initializes MTU discovery for a newly validated path and starts probing. |
| `BOOLEAN` | `QuicMtuDiscoveryOnAckedPacket` | Handles ACK of MTU probe; updates path MTU and either continues probing or completes search. |
| `void` | `QuicMtuDiscoveryProbePacketDiscarded` | Handles lost/discarded MTU probe; retries same size or completes search after max misses. |

# QUIC operation
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicOperLog` | Logs operation execution details (special handling for API and timer operations). |
| `void` | `QuicOperationQueueInitialize` | Initializes a connection operation queue, lock, list, and priority boundary. |
| `void` | `QuicOperationQueueUninitialize` | Uninitializes operation queue (expects queue already empty). |
| `QUIC_OPERATION*` | `QuicOperationAlloc` | Allocates an operation from partition pool; allocates API context when type is API call. |
| `void` | `QuicOperationFree` | Frees an operation and releases/cleans all associated resources by operation/API subtype. |
| `BOOLEAN` | `QuicOperationHasPriority` | Returns whether priority operations are currently queued. |
| `BOOLEAN` | `QuicOperationEnqueue` | Enqueues normal-priority operation; returns whether processing should start now. |
| `BOOLEAN` | `QuicOperationEnqueuePriority` | Enqueues operation into priority segment; returns whether processing should start now. |
| `BOOLEAN` | `QuicOperationEnqueueFront` | Enqueues operation at queue front; returns whether processing should start now. |
| `QUIC_OPERATION*` | `QuicOperationDequeue` | Dequeues next operation; updates active-processing and priority-tail state. |
| `void` | `QuicOperationQueueClear` | Drains and clears queued operations, completing/aborting pending work as appropriate. |

# QUIC packet builder
| Return Type | Function Name | Description |
|---|---|---|
| `BOOLEAN` | `QuicPacketBuilderInitialize` | Initializes packet builder state for a connection/path and computes initial send allowance. |
| `void` | `QuicPacketBuilderCleanup` | Cleans remaining builder state after send loop (timers/metadata/crypto mask cleanup). |
| `BOOLEAN` | `QuicPacketBuilderPrepare` | Internal core prepare routine that allocates/reuses datagram space and starts a new QUIC packet header/key context. |
| `BOOLEAN` | `QuicPacketBuilderGetPacketTypeAndKeyForControlFrames` | Chooses packet key/type for control traffic based on send flags and handshake/key availability. |
| `BOOLEAN` | `QuicPacketBuilderPrepareForControlFrames` | Prepares builder to encode control frames. |
| `BOOLEAN` | `QuicPacketBuilderPrepareForPathMtuDiscovery` | Prepares builder to encode/send a PMTUD probe packet. |
| `BOOLEAN` | `QuicPacketBuilderPrepareForStreamFrames` | Prepares builder to encode stream data frames (0-RTT or 1-RTT as appropriate). |
| `void` | `QuicPacketBuilderFinalizeHeaderProtection` | Applies batched header protection masks to queued short-header packets. |
| `BOOLEAN` | `QuicPacketBuilderFinalize` | Finalizes current packet/datagram (padding, encryption, header protection, tracking, optional flush/send). |
| `void` | `QuicPacketBuilderSendBatch` | Sends accumulated datagram batch via binding and resets batch state. |
| `BOOLEAN` | `QuicPacketBuilderHasAllowance` | Inline helper: returns whether congestion/exemption allows more sending. |
| `BOOLEAN` | `QuicPacketBuilderAddFrame` | Inline helper: appends frame metadata and returns whether frame metadata array is full. |
| `BOOLEAN` | `QuicPacketBuilderAddStreamFrame` | Inline helper: appends stream frame metadata and increments stream sent-metadata tracking. |
| `void` | `QuicPacketBuilderValidate` | Debug-only invariant checker for builder internal state correctness. |
| `void` | `QuicFuzzInjectHook` | Fuzzer hook declaration used to mutate pre-encryption payload during fuzzing builds. |

# QUIC packet space
| Return Type | Function Name | Description |
|---|---|---|
| `QUIC_PACKET_KEY_TYPE` | `QuicEncryptLevelToKeyType` | Maps packet encryption level (`Initial`, `Handshake`, `1-RTT`) to packet key type. |
| `QUIC_ENCRYPT_LEVEL` | `QuicKeyTypeToEncryptLevel` | Maps packet key type (including `0-RTT`) to encryption level. |
| `QUIC_PACKET_SPACE*` | `QuicAckTrackerGetPacketSpace` | Returns owning packet-space object from an ACK tracker pointer. |
| `QUIC_STATUS` | `QuicPacketSpaceInitialize` | Allocates and initializes a packet-space instance and its ACK tracker. |
| `void` | `QuicPacketSpaceUninitialize` | Releases deferred packets, uninitializes ACK tracker, and frees packet-space memory. |
| `void` | `QuicPacketSpaceReset` | Resets packet-space ACK tracking state. |

# QUIC packet
| Return Type | Function Name | Description |
|---|---|---|
| `BOOLEAN` | `QuicPacketValidateInvariant` | Validates QUIC invariant header fields, parses/caches source and destination CIDs, and rejects malformed packets early. |
| `BOOLEAN` | `QuicPacketIsHandshake` | Checks whether a packet is a handshake-class packet (long header and not 0-RTT). |
| `BOOLEAN` | `QuicPacketValidateLongHeaderV1` | Validates version-specific long header fields (types, fixed bit, token/length varints) and sets header/payload boundaries. |
| `void` | `QuicPacketDecodeRetryTokenV1` | Extracts token pointer and length from an already-validated Initial packet. |
| `BOOLEAN` | `QuicPacketValidateInitialToken` | Decrypts and validates Retry token contents (format/address/length) for Initial packet acceptance. |
| `BOOLEAN` | `QuicPacketValidateShortHeaderV1` | Validates short-header fixed bit and initializes payload/header interpretation before PN deprotection. |
| `void` | `QuicPktNumEncode` | Encodes a packet number into 1–4 network-order bytes. |
| `void` | `QuicPktNumDecode` | Decodes a 1–4 byte packet number field into host-order value. |
| `uint64_t` | `QuicPktNumDecompress` | Reconstructs full packet number from truncated bytes using expected next packet number. |
| `uint16_t` | `QuicPacketEncodeLongHeaderV1` | Builds a QUIC v1/v2 long header (including optional Initial token) into an output buffer. |
| `QUIC_STATUS` | `QuicPacketGenerateRetryIntegrity` | Derives Retry integrity key and computes the Retry integrity tag over pseudo-packet data. |
| `uint16_t` | `QuicPacketEncodeRetryV1` | Encodes a Retry packet (header, token, integrity tag) for QUIC v1/v2. |
| `uint16_t` | `QuicPacketEncodeShortHeaderV1` | Encodes a short header with CID, flags, and packet number bytes. |
| `uint32_t` | `QuicPacketHash` | Computes a routing hash from remote address and remote CID (Toeplitz-based). |
| `_Null_terminated_ const char*` | `QuicLongHeaderTypeToStringV1` | Converts QUIC v1 long-header type enum to short log string. |
| `_Null_terminated_ const char*` | `QuicLongHeaderTypeToStringV2` | Converts QUIC v2 long-header type enum to short log string. |
| `void` | `QuicPacketLogHeader` | Emits structured trace logs for parsed packet headers (long/short/version-negotiation/retry). |
| `void` | `QuicPacketLogDrop` | Logs packet drop reason and updates drop counters/perf metrics. |
| `void` | `QuicPacketLogDropWithValue` | Logs packet drop reason with numeric context value and updates counters/metrics. |

# QUIC partition
| Return Type | Function Name | Description |
|---|---|---|
| `QUIC_STATUS` | `QuicPartitionInitialize` | Initializes a partition: reset-token hash, object pools, sent-packet pool, and synchronization primitives. |
| `void` | `QuicPartitionUninitialize` | Frees partition resources: retry keys, pools, locks, and reset-token hash. |
| `CXPLAT_KEY*` | `QuicPartitionGetStatelessRetryKey` | Internal helper that returns/rotates a per-partition stateless-retry key for a specific key index (deriving it if missing). |
| `CXPLAT_KEY*` | `QuicPartitionGetCurrentStatelessRetryKey` | Gets the currently active stateless-retry key based on current time and key-rotation interval. |
| `CXPLAT_KEY*` | `QuicPartitionGetStatelessRetryKeyForTimestamp` | Gets a retry key valid for a token timestamp (accepting only current or previous rotation window). |
| `QUIC_STATUS` | `QuicPartitionUpdateStatelessResetKey` | Replaces the partition’s stateless-reset hash key atomically by creating a new hash context and swapping under lock. |
| `void` | `QuicPerfCounterAdd` | Atomically adds a value to a per-partition performance counter. |

# QUIC path
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicPathInitialize` | Initializes a path slot with defaults (ID, RTT state, MTU, ECN state, optional QTIP sequence seed). |
| `void` | `QuicPathRemove` | Removes a path from the connection’s path array and compacts remaining entries. |
| `void` | `QuicPathSetAllowance` | Updates anti-amplification send allowance and toggles flow-blocked state/timers accordingly. |
| `void` | `QuicPathIncrementAllowance` | Inline helper to increase path allowance via `QuicPathSetAllowance`. |
| `void` | `QuicPathDecrementAllowance` | Inline helper to decrease path allowance (clamped at zero) via `QuicPathSetAllowance`. |
| `uint16_t` | `QuicPathGetDatagramPayloadSize` | Computes max UDP payload size for the path based on address family and path MTU. |
| `void` | `QuicPathSetValid` | Marks peer address as validated for a path, removes amplification limit, and may trigger MTU discovery flow. |
| `void` | `QuicPathSetActive` | Promotes a path to active, swaps path state as needed, and resets congestion control when appropriate. |
| `QUIC_PATH*` | `QuicConnGetPathByID` | Finds and returns a path by path ID, also returning its index. |
| `QUIC_PATH*` | `QuicConnGetPathForPacket` | Matches incoming packet route to existing path or creates/tracks a new path if valid. |
| `void` | `QuicCopyRouteInfo` | Copies route metadata from one route object to another. |
| `void` | `QuicPathUpdateQeo` | Adds or removes QUIC encryption offload (QEO) state/keys for the path on the socket. |

# QUIC precomp

# QUIC quicdef

# QUIC range
| Return Type | Function Name | Description |
|---|---|---|
| `uint64_t` | `QuicRangeGetHigh` | Returns the highest value covered by a subrange (`Low + Count - 1`). |
| `uint32_t` | `QuicRangeSize` | Returns the number of active subranges in the range set. |
| `QUIC_SUBRANGE*` | `QuicRangeGet` | Returns subrange pointer at a specific index (no bounds check). |
| `QUIC_SUBRANGE*` | `QuicRangeGetSafe` | Returns subrange pointer at index, or `NULL` if out of bounds. |
| `int` | `QuicRangeCompare` | Compares a search key to a subrange (`-1` before, `0` overlap, `1` after). |
| `int` | `QuicRangeSearch` | Finds overlapping subrange (or encoded insertion position) via binary/linear search mode. |
| `void` | `QuicRangeInitialize` | Initializes range state with preallocated storage and allocation limits. |
| `void` | `QuicRangeUninitialize` | Frees dynamically allocated subrange storage when used. |
| `void` | `QuicRangeReset` | Clears all stored values and resets used length. |
| `BOOLEAN` | `QuicRangeGrow` | Internal helper that expands subrange capacity and preserves insertion layout. |
| `QUIC_SUBRANGE*` | `QuicRangeMakeSpace` | Internal helper that creates insertion space for a new subrange, growing or shifting as needed. |
| `BOOLEAN` | `QuicRangeRemoveSubranges` | Removes contiguous subranges and may shrink backing storage. |
| `BOOLEAN` | `QuicRangeGetRange` | Retrieves contiguous count starting at a value and indicates whether it is the last subrange. |
| `QUIC_SUBRANGE*` | `QuicRangeAddRange` | Inserts/merges a contiguous value range into the set and returns the updated subrange. |
| `BOOLEAN` | `QuicRangeAddValue` | Inserts a single value into the range set. |
| `BOOLEAN` | `QuicRangeRemoveRange` | Removes a contiguous value range, splitting/trimming subranges as needed. |
| `void` | `QuicRangeSetMin` | Drops all values below a new minimum threshold. |
| `uint64_t` | `QuicRangeGetMin` | Returns minimum value in the set (expects non-empty range). |
| `BOOLEAN` | `QuicRangeGetMinSafe` | Returns minimum value if present; otherwise returns `FALSE`. |
| `uint64_t` | `QuicRangeGetMax` | Returns maximum value in the set (expects non-empty range). |
| `BOOLEAN` | `QuicRangeGetMaxSafe` | Returns maximum value if present; otherwise returns `FALSE`. |

# QUIC recv buffer
| Return Type | Function Name | Description |
|---|---|---|
| `QUIC_RECV_CHUNK_ITERATOR` | `QuicRecvBufferGetChunkIterator` | Internal helper that builds a chunk iterator starting at a given logical offset in the receive buffer. |
| `BOOLEAN` | `QuicRecvChunkIteratorNext` | Internal helper that returns the next contiguous readable span from chunk(s), optionally marking chunk as externally referenced. |
| `void` | `QuicRecvChunkInitialize` | Initializes a receive chunk descriptor (length, buffer pointer, ownership/reference flags). |
| `void` | `QuicRecvChunkFree` | Frees a receive chunk using pool/free path based on allocation source. |
| `void` | `QuicRecvBufferValidate` | Debug-only invariant checker for receive-buffer mode/state consistency. |
| `QUIC_STATUS` | `QuicRecvBufferInitialize` | Initializes receive-buffer state and initial chunk (except app-owned mode). |
| `void` | `QuicRecvBufferUninitialize` | Releases written-range state and all owned/retired chunks. |
| `uint64_t` | `QuicRecvBufferGetTotalLength` | Returns highest written stream length (from offset 0). |
| `uint32_t` | `QuicRecvBufferGetSpan` | Internal helper that returns current occupied span (`totalLength - baseOffset`). |
| `BOOLEAN` | `QuicRecvBufferHasUnreadData` | Returns whether new contiguous unread data is available to deliver. |
| `void` | `QuicRecvBufferIncreaseVirtualBufferLength` | Increases virtual receive window size (never decreases). |
| `uint32_t` | `QuicRecvBufferGetTotalAllocLength` | Internal helper that sums effective capacity across chunks. |
| `QUIC_STATUS` | `QuicRecvBufferProvideChunks` | Adds app-provided chunks in app-owned mode and updates capacity/virtual size. |
| `BOOLEAN` | `QuicRecvBufferResize` | Internal helper to grow buffer capacity by adding/replacing chunks and copying data if needed. |
| `void` | `QuicRecvBufferCopyIntoChunks` | Internal helper that copies written bytes into the correct chunk ranges and updates read length. |
| `QUIC_STATUS` | `QuicRecvBufferWrite` | Validates quota/capacity, tracks written ranges, and buffers incoming stream data (out-of-order/duplicate safe). |
| `uint32_t` | `QuicRecvBufferReadBufferNeededCount` | Returns how many `QUIC_BUFFER` entries are needed to expose current readable data. |
| `void` | `QuicRecvBufferRead` | Exposes pointers/lengths of contiguous readable data segments and updates pending-read tracking. |
| `void` | `QuicRecvBufferDrainFullChunks` | Internal helper that fully drains/removes consumed chunks and updates first-chunk state. |
| `void` | `QuicRecvBufferDrainFirstChunk` | Internal helper that partially drains first chunk by adjusting read start/capacity (and compacts in single mode). |
| `BOOLEAN` | `QuicRecvBufferDrain` | Marks delivered bytes drained, frees/rotates chunk state, updates references, and returns whether buffer is now empty. |
| `void` | `QuicRecvBufferResetRead` | Cancels pending read state (single-buffer mode). |

# QUIC registration
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicRegistrationClose` | Internal close routine that tears down a registration (waits rundown, stops worker pool, removes from global lists, releases resources). |
| `CXPLAT_THREAD_CALLBACK` | `RegistrationCloseWorker` | Background thread entry used for asynchronous registration close and cleanup handoff. |
| `QUIC_STATUS` | `MsQuicRegistrationOpen` | Public API to create and initialize a registration (state, worker pool, close thread, global tracking). |
| `void` | `MsQuicRegistrationClose` | Public synchronous registration close API. |
| `void` | `MsQuicRegistrationClose2` | Public asynchronous close API that signals close and invokes a completion callback. |
| `void` | `MsQuicRegistrationShutdown` | Public API that initiates shutdown across all registration connections/listeners. |
| `BOOLEAN` | `QuicRegistrationRundownAcquire` | Acquires a registration rundown reference (debug builds also track ref type). |
| `void` | `QuicRegistrationRundownRelease` | Releases a registration rundown reference (and debug ref tracking). |
| `void` | `QuicRegistrationTraceRundown` | Emits rundown tracing for registration and child objects (configs/connections). |
| `void` | `QuicRegistrationSettingsChanged` | Propagates global settings changes to all configurations in the registration. |
| `BOOLEAN` | `QuicRegistrationAcceptConnection` | Decides if a new connection should be accepted based on selected worker load. |
| `void` | `QuicRegistrationQueueNewConnection` | Assigns a new connection to the worker determined by partition policy. |
| `QUIC_STATUS` | `QuicRegistrationParamSet` | Sets registration-level parameters (currently returns invalid parameter). |
| `QUIC_STATUS` | `QuicRegistrationParamGet` | Gets registration-level parameters (currently returns invalid parameter). |

# QUIC send buffer
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicSendBufferInitialize` | Initializes send-buffer state, including default ideal buffered-byte target. |
| `void` | `QuicSendBufferUninitialize` | Uninitializes send-buffer state (no-op cleanup). |
| `uint8_t*` | `QuicSendBufferAlloc` | Allocates memory for buffered send data and increments buffered-byte accounting. |
| `void` | `QuicSendBufferFree` | Frees buffered send memory and decrements buffered-byte accounting. |
| `BOOLEAN` | `QuicSendBufferHasSpace` | Internal helper that checks whether buffered bytes are below ideal target. |
| `void` | `QuicSendBufferFill` | Buffers pending stream send requests (in-order per stream) until ideal buffer target is reached. |
| `uint32_t` | `QuicGetNextIdealBytes` | Internal helper that rounds a base byte value up to the next ideal send-buffer threshold (1.5x growth steps). |
| `void` | `QuicSendBufferStreamAdjust` | Computes and indicates per-stream ideal send buffer size to the app. |
| `void` | `QuicSendBufferConnectionAdjust` | Recomputes connection ideal send-buffer target from congestion state, updates streams, and refills buffering if enabled. |

# QUIC send
| Return Type | Function Name | Description |
|---|---|---|
| `uint8_t` | `QuicKeyTypeToPacketTypeV1` | Maps packet key type to QUIC v1 packet type. |
| `uint8_t` | `QuicKeyTypeToPacketTypeV2` | Maps packet key type to QUIC v2 packet type. |
| `QUIC_PACKET_KEY_TYPE` | `QuicPacketTypeToKeyTypeV1` | Maps QUIC v1 packet type to packet key type. |
| `QUIC_PACKET_KEY_TYPE` | `QuicPacketTypeToKeyTypeV2` | Maps QUIC v2 packet type to packet key type. |
| `uint8_t` | `QuicEncryptLevelToPacketTypeV1` | Maps encryption level to QUIC v1 packet type. |
| `uint8_t` | `QuicEncryptLevelToPacketTypeV2` | Maps encryption level to QUIC v2 packet type. |
| `QUIC_ENCRYPT_LEVEL` | `QuicPacketTypeToEncryptLevelV1` | Maps QUIC v1 packet type to encryption level. |
| `QUIC_ENCRYPT_LEVEL` | `QuicPacketTypeToEncryptLevelV2` | Maps QUIC v2 packet type to encryption level. |
| `BOOLEAN` | `HasStreamControlFrames` | Checks if stream send flags include any control-frame flags. |
| `BOOLEAN` | `HasStreamDataFrames` | Checks if stream send flags include data/open/fin flags. |
| `void` | `QuicSendInitialize` | Initializes send state, flow-control max, and randomized packet-number start/skip values. |
| `void` | `QuicSendUninitialize` | Marks send module uninitialized, frees initial token, and releases queued stream send refs. |
| `void` | `QuicSendApplyNewSettings` | Applies updated connection flow-control window to send state. |
| `void` | `QuicSendReset` | Resets send flags/timestamps and cancels ACK-delay and pacing timers. |
| `BOOLEAN` | `QuicSendCanSendFlagsNow` | Determines whether queued connection-level send flags are currently allowed to be sent. |
| `void` | `QuicSendQueueFlush` | Queues a `FLUSH_SEND` operation if not already pending and sending is currently allowed. |
| `void` | `QuicSendQueueFlushForStream` | Queues/prioritizes a stream in send list and optionally triggers immediate flush. |
| `void` | `QuicSendUpdateStreamPriority` | Reorders an already-queued stream in send list after priority change. |
| `void` | `QuicSendValidate` | Debug-only invariant validation for ACK/send-flag state. |
| `void` | `QuicSendClear` | Clears connection send flags (except allowed post-close) and empties queued stream send work. |
| `BOOLEAN` | `QuicSendSetSendFlag` | Sets connection send flags, schedules flush, and handles close-frame special behavior. |
| `void` | `QuicSendClearSendFlag` | Clears specified connection send flags. |
| `void` | `QuicSendUpdateAckState` | Reconciles ACK send flag and delayed-ACK timer with current ack-tracker state. |
| `BOOLEAN` | `QuicSendSetStreamSendFlag` | Sets stream send flags after state filtering and queues the stream for sending. |
| `void` | `QuicSendClearStreamSendFlag` | Clears stream send flags and dequeues stream if no work remains. |
| `BOOLEAN` | `QuicSendWriteFrames` | Writes connection-level frames into current packet builder in priority order. |
| `BOOLEAN` | `QuicSendCanSendStreamNow` | Checks whether a stream is currently eligible to write frames. |
| `QUIC_STREAM*` | `QuicSendGetNextStream` | Selects next stream to send based on queue order and prioritization policy. |
| `BOOLEAN` | `CxPlatIsRouteReady` | Checks route readiness and initiates route resolution when needed. |
| `void` | `QuicSendPathChallenges` | Sends pending PATH_CHALLENGE frames on eligible paths. |
| `BOOLEAN` | `QuicSendFlush` | Main send loop that builds/finalizes datagrams for control, PMTU, and stream data; returns whether fully drained. |
| `void` | `QuicSendStartDelayedAckTimer` | Starts delayed ACK timer when ACK can be deferred. |
| `void` | `QuicSendProcessDelayedAckTimer` | Handles delayed ACK timeout and schedules ACK sending if still needed. |

# QUIC send packet metadata
| Return Type | Function Name | Description |
|---|---|---|
| `uint8_t` | `QuicPacketTraceType` | Maps packet key type in sent metadata to trace packet type enum/value. |
| `void` | `QuicSentPacketMetadataReleaseFrames` | Releases per-frame resources for a sent packet metadata item (stream refs, datagram send-state callbacks). |
| `void` | `QuicSentPacketPoolInitialize` | Initializes size-bucketed pools for sent packet metadata allocations by frame count. |
| `void` | `QuicSentPacketPoolUninitialize` | Uninitializes all sent packet metadata pools. |
| `QUIC_SENT_PACKET_METADATA*` | `QuicSentPacketPoolGetPacketMetadata` | Allocates a sent packet metadata object sized for the requested frame count. |
| `void` | `QuicSentPacketPoolReturnPacketMetadata` | Cleans up frame-associated state and returns sent packet metadata to pool. |

# QUIC settings
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicSettingsSetDefault` | Fills unset internal settings fields with MsQuic default values. |
| `void` | `QuicSettingsCopy` | Copies parent/source settings into destination only for fields not explicitly set. |
| `QUIC_VERSION_SETTINGS*` | `QuicSettingsCopyVersionSettings` | Internal helper that deep-copies version-settings arrays (optionally converts external byte order to internal). |
| `BOOLEAN` | `QuicSettingApply` | Applies a settings delta into destination with validation and overwrite policy controls. |
| `void` | `QuicSettingsCleanup` | Frees dynamically allocated settings-owned memory (notably version settings). |
| `void` | `QuicSettingsLoad` | Loads settings from persistent storage for fields not already set by caller. |
| `void` | `QuicSettingsDump` | Logs all effective internal settings values. |
| `void` | `QuicSettingsDumpNew` | Logs only settings that are marked as explicitly set. |
| `QUIC_STATUS` | `QuicSettingsSettingsToInternal` | Converts public `QUIC_SETTINGS` into validated internal settings representation. |
| `QUIC_STATUS` | `QuicSettingsGlobalSettingsToInternal` | Converts public `QUIC_GLOBAL_SETTINGS` into internal settings representation. |
| `QUIC_STATUS` | `QuicSettingsVersionSettingsToInternal` | Converts and validates public `QUIC_VERSION_SETTINGS` into internal format. |
| `QUIC_STATUS` | `QuicSettingsGetSettings` | Exports internal settings into public `QUIC_SETTINGS` buffer (with size negotiation). |
| `QUIC_STATUS` | `QuicSettingsGetGlobalSettings` | Exports internal settings into public `QUIC_GLOBAL_SETTINGS` buffer. |
| `QUIC_STATUS` | `QuicSettingsGetVersionSettings` | Exports internal version settings into public `QUIC_VERSION_SETTINGS` buffer and inline arrays. |

# QUIC sliding window extream
| Return Type | Function Name | Description |
|---|---|---|
| `QUIC_SLIDING_WINDOW_EXTREMUM` | `QuicSlidingWindowExtremumInitialize` | Initializes a sliding-window extremum instance (lifetime, capacity, backing entries, and indices). |
| `QUIC_STATUS` | `QuicSlidingWindowExtremumGet` | Returns current extremum entry from the window head, or `QUIC_STATUS_NOT_FOUND` if empty. |
| `void` | `QuicSlidingWindowExtremumReset` | Resets window state to empty without changing configuration. |
| `void` | `SlidingWindowExtremumExpire` | Internal helper that removes expired entries from the head based on entry lifetime. |
| `void` | `QuicSlidingWindowExtremumUpdateMin` | Inserts a new sample and maintains monotonic queue for sliding-window minimum tracking. |
| `void` | `QuicSlidingWindowExtremumUpdateMax` | Inserts a new sample and maintains monotonic queue for sliding-window maximum tracking. |

# QUIC stream recv
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicStreamRecvShutdown` | Shuts down the stream receive path (silent or signaled), updates flags, and queues/clears related control frames. |
| `void` | `QuicStreamRecvQueueFlush` | Queues (or runs inline) a receive flush when data/FIN is pending for app delivery. |
| `void` | `QuicStreamIndicatePeerSendAbortedEvent` | Indicates `QUIC_STREAM_EVENT_PEER_SEND_ABORTED` to the app with peer error code. |
| `void` | `QuicStreamProcessReliableResetFrame` | Handles `RELIABLE_RESET_STREAM` semantics, including validation and deferred/inline receive shutdown behavior. |
| `void` | `QuicStreamProcessResetFrame` | Handles `RESET_STREAM`, validates final size/flow-control consistency, updates state, and triggers shutdown signaling. |
| `void` | `QuicStreamProcessStopSendingFrame` | Handles `STOP_SENDING` from peer and triggers local send-side abort flow. |
| `QUIC_STATUS` | `QuicStreamProcessStreamFrame` | Validates/processes incoming `STREAM` data/FIN, writes into recv buffer, updates flow-control accounting, and queues delivery. |
| `QUIC_STATUS` | `QuicStreamRecv` | Top-level receive-frame dispatcher for stream-related frames (`STREAM`, `RESET_STREAM`, `STOP_SENDING`, etc.). |
| `void` | `QuicStreamOnBytesDelivered` | Updates stream/connection receive flow-control windows after app drains data and schedules MAX_DATA/MAX_STREAM_DATA as needed. |
| `void` | `QuicStreamRecvFlush` | Delivers pending receive data/FIN callbacks to app, handles callback result semantics, and advances completion flow. |
| `void` | `QuicStreamReceiveCompletePending` | Completes deferred receive-complete processing from operation queue and optionally continues flush. |
| `BOOLEAN` | `QuicStreamReceiveComplete` | Applies app-consumed byte count to recv buffer/state and determines whether another receive callback should run. |
| `QUIC_STATUS` | `QuicStreamRecvSetEnabledState` | Enables/disables receive callbacks and triggers flush when receive is re-enabled with pending data. |

# QUIC stream send
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicStreamValidateRecoveryState` | Debug-only check that recovery window boundaries do not overlap acknowledged sparse ranges. |
| `void` | `QuicStreamIndicateSendShutdownComplete` | Raises `QUIC_STREAM_EVENT_SEND_SHUTDOWN_COMPLETE` once for the stream send path. |
| `void` | `QuicStreamSendShutdown` | Performs graceful/abortive/reliable send shutdown, updates flags, queues reset/fin frames, and cancels queued sends as needed. |
| `BOOLEAN` | `QuicStreamAllowedByPeer` | Checks stream ID flow-control allowance against peer-advertised stream count limits. |
| `BOOLEAN` | `QuicStreamHasPendingStreamData` | Returns whether stream has unsent or recovery-window data pending. |
| `BOOLEAN` | `QuicStreamHasPending0RttData` | Returns whether stream still has data/FIN eligible for 0-RTT sending. |
| `BOOLEAN` | `QuicStreamSendCanWriteDataFrames` | Checks whether stream data frames can currently be written (flow-control/recovery aware). |
| `BOOLEAN` | `QuicStreamCanSendNow` | Determines if stream currently has any sendable control/data work (optionally constrained to 0-RTT). |
| `void` | `QuicStreamCompleteSendRequest` | Completes/cancels a send request, emits completion event, frees buffered memory, and updates send-buffer accounting. |
| `QUIC_STATUS` | `QuicStreamSendBufferRequest` | Copies app send payload into internal send buffer for buffered-send mode and completes request early. |
| `void` | `QuicStreamEnqueueSendRequest` | Moves request into stream send queue, assigns stream offset, and updates bookmarks/0-RTT bookkeeping. |
| `void` | `QuicStreamSendFlush` | Drains API-queued send requests into stream queue, handles start/flags/buffering, and schedules send work. |
| `BOOLEAN` | `QuicStreamCopyFromSendRequests` | Copies queued stream bytes into packet buffer from send requests at a target offset/length. |
| `BOOLEAN` | `QuicStreamWriteOneFrame` | Encodes one STREAM frame (header+payload), updates metadata/offsets, and writes to packet buffer. |
| `void` | `QuicStreamWriteStreamFrames` | Writes one or more STREAM frames for normal send/recovery paths into current packet payload budget. |
| `BOOLEAN` | `QuicStreamSendWrite` | Writes stream-related frames (MAX_STREAM_DATA/RESET/STOP_SENDING/STREAM/etc.) into packet builder. |
| `BOOLEAN` | `QuicStreamOnLoss` | Handles loss of stream frames by updating recovery ranges/flags and re-queuing retransmission work. |
| `void` | `QuicStreamOnAck` | Processes ACKed stream frame metadata, advances UNA/SACK state, completes send requests, and finalizes send shutdown when eligible. |
| `void` | `QuicStreamCancelRequests` | Cancels all queued send requests for the stream. |
| `void` | `QuicStreamOnResetAck` | Handles ACK of RESET_STREAM by marking send side acknowledged and completing shutdown path. |
| `void` | `QuicStreamOnResetReliableAck` | Handles ACK of RELIABLE_RESET_STREAM and completes shutdown once reliable offset conditions are met. |
| `void` | `QuicStreamSendDumpState` | Emits verbose diagnostics of stream send/recovery/SACK state. |

# QUIC stream set
| Return Type | Function Name | Description |
|---|---|---|
| `void` | `QuicStreamSetValidate` | Debug-only validator for stream-set container consistency (hash table and waiting list membership). |
| `void` | `QuicStreamSetInitialize` | Initializes stream-set lists and debug tracking state. |
| `void` | `QuicStreamSetUninitialize` | Uninitializes stream-set resources (notably stream hash table). |
| `void` | `QuicStreamSetTraceRundown` | Emits rundown tracing for active and waiting streams. |
| `BOOLEAN` | `QuicStreamSetLazyInitStreamTable` | Lazily allocates/initializes the stream hash table. |
| `BOOLEAN` | `QuicStreamSetInsertStream` | Inserts a stream into the stream table and marks table membership. |
| `QUIC_STREAM*` | `QuicStreamSetLookupStream` | Looks up an active stream by stream ID in the stream table. |
| `void` | `QuicStreamSetShutdown` | Silently abort-shuts down all streams in active and waiting containers. |
| `void` | `QuicStreamSetReleaseStream` | Removes a stream from active/waiting containers, queues it for closed-drain cleanup, and updates stream-count/`MAX_STREAMS` signaling. |
| `void` | `QuicStreamSetDrainClosedStreams` | Final cleanup pass that releases stream-set references for closed streams. |
| `void` | `QuicStreamSetIndicateStreamsAvailable` | Internal helper that indicates updated available stream counts to app (`STREAMS_AVAILABLE` event). |
| `void` | `QuicStreamIndicatePeerAccepted` | Internal helper that raises `QUIC_STREAM_EVENT_PEER_ACCEPTED` when configured. |
| `void` | `QuicStreamSetInitializeTransportParameters` | Applies peer transport-parameter stream limits, unblocks waiting streams, updates per-stream FC, and optionally queues send flush. |
| `void` | `QuicStreamSetUpdateMaxStreams` | Handles peer `MAX_STREAMS` updates and unblocks newly allowed local streams. |
| `void` | `QuicStreamSetUpdateMaxCount` | Applies app-configured max concurrent stream counts and triggers `MAX_STREAMS` signaling when needed. |
| `uint16_t` | `QuicStreamSetGetCountAvailable` | Returns currently available stream-open credits for a stream type. |
| `void` | `QuicStreamSetGetFlowControlSummary` | Aggregates stream-level send flow-control availability and send-window estimates across active streams. |
| `QUIC_STATUS` | `QuicStreamSetNewLocalStream` | Creates/registers a new local stream or queues it in waiting list when blocked by stream-ID flow control. |
| `QUIC_STREAM*` | `QuicStreamSetGetStreamForPeer` | Looks up or creates peer-initiated streams by ID, with protocol validation and app notification. |
| `void` | `QuicStreamSetGetMaxStreamIDs` | Computes per-type max stream IDs from current `MaxTotalStreamCount` values. |

# QUIC stream
| Return Type | Function Name | Description |
|---|---|---|
| `QUIC_STREAM_SEND_STATE` | `QuicStreamSendGetState` | Computes current send-side state enum from stream flags. |
| `QUIC_STREAM_RECV_STATE` | `QuicStreamRecvGetState` | Computes current receive-side state enum from stream flags. |
| `uint64_t` | `QuicStreamGetInitialMaxDataFromTP` | Returns initial per-stream flow-control limit from peer transport parameters based on stream type/direction. |
| `void` | `QuicStreamAddRef` | Adds a reference to the stream object (with debug ref-type accounting). |
| `BOOLEAN` | `QuicStreamRelease` | Releases a stream reference and frees stream when refcount reaches zero. |
| `void` | `QuicStreamSentMetadataIncrement` | Increments outstanding sent-metadata count and takes send-packet ref on first item. |
| `void` | `QuicStreamSentMetadataDecrement` | Decrements outstanding sent-metadata count and releases send-packet ref when it reaches zero. |
| `BOOLEAN` | `QuicStreamAddOutFlowBlockedReason` | Adds a stream outflow-block reason and starts corresponding blocked-time tracking. |
| `BOOLEAN` | `QuicStreamRemoveOutFlowBlockedReason` | Removes a stream outflow-block reason and accumulates blocked-time duration. |
| `QUIC_STATUS` | `QuicStreamInitialize` | Allocates and initializes stream object state, refs, recv buffer mode, and flow-control defaults. |
| `void` | `QuicStreamFree` | Final stream cleanup/free path; releases internal resources and connection ref. |
| `QUIC_STATUS` | `QuicStreamStart` | Starts stream, assigns/validates stream ID, initializes flow-control/send-open behavior, and indicates start completion. |
| `void` | `QuicStreamClose` | Closes app handle, triggers immediate abort shutdown if needed, and releases app reference. |
| `void` | `QuicStreamTraceRundown` | Emits rundown diagnostics for stream identity and blocked-state info. |
| `QUIC_STATUS` | `QuicStreamIndicateEvent` | Invokes app stream callback for a stream event with reentrancy safety checks. |
| `void` | `QuicStreamIndicateStartComplete` | Raises `QUIC_STREAM_EVENT_START_COMPLETE` once with status/ID/peer-accepted details. |
| `void` | `QuicStreamIndicateShutdownComplete` | Internal helper to raise `QUIC_STREAM_EVENT_SHUTDOWN_COMPLETE` once and detach callback handler. |
| `void` | `QuicStreamShutdown` | Top-level stream shutdown dispatcher for graceful/abort-send/abort-receive/immediate modes. |
| `void` | `QuicStreamTryCompleteShutdown` | Completes shutdown when both directions are acknowledged and no pending receive data remains. |
| `QUIC_STATUS` | `QuicStreamParamSet` | Sets stream parameters (priority, reliable offset) with validation. |
| `QUIC_STATUS` | `QuicStreamParamGet` | Gets stream parameters/statistics with size negotiation and state checks. |
| `void` | `QuicStreamSwitchToAppOwnedBuffers` | Switches stream receive buffer to app-owned mode while preserving flow-control window. |
| `QUIC_STATUS` | `QuicStreamProvideRecvBuffers` | Supplies app-owned recv chunks and updates max-recv-offset signaling if window grows. |
| `void` | `QuicStreamNotifyReceiveBufferNeeded` | Indicates `QUIC_STREAM_EVENT_RECEIVE_BUFFER_NEEDED` to app for app-owned buffering mode. |

# QUIC timer wheel
| Return Type | Function Name | Description |
|---|---|---|
| `QUIC_STATUS` | `QuicTimerWheelInitialize` | Initializes timer-wheel state and allocates initial slot array. |
| `void` | `QuicTimerWheelUninitialize` | Validates/cleans up timer-wheel slots and frees slot storage. |
| `void` | `QuicTimerWheelResize` | Internal helper that doubles slot count and rehashes/reorders existing connection timer entries. |
| `void` | `QuicTimerWheelUpdate` | Internal helper that recomputes global next-expiring connection/time across all slots. |
| `void` | `QuicTimerWheelRemoveConnection` | Removes a connection from the wheel, updates next-expiration if needed, and releases timer-wheel ref. |
| `void` | `QuicTimerWheelUpdateConnection` | Inserts/moves/removes a connection in the wheel based on its current earliest timer expiration. |
| `void` | `QuicTimerWheelGetExpired` | Extracts all connections with expired timers into output list and updates refs/next-expiration state. |

# QUIC version neg
| Return Type | Function Name | Description |
|---|---|---|
| `BOOLEAN` | `QuicVersionNegotiationExtIsVersionServerSupported` | Checks whether a version is server-supported (using configured acceptable versions or default support set). |
| `BOOLEAN` | `QuicVersionNegotiationExtIsVersionClientSupported` | Checks whether a version is client-supported (using connection fully-deployed versions or default support set). |
| `BOOLEAN` | `QuicVersionNegotiationExtAreVersionsCompatible` | Tests whether an original version is compatible with an upgraded/negotiated version using the compatibility map. |
| `BOOLEAN` | `QuicVersionNegotiationExtIsVersionCompatible` | Checks whether negotiated version is compatible with the connection’s configured/default compatibility list. |
| `QUIC_STATUS` | `QuicVersionNegotiationExtGenerateCompatibleVersionsList` | Builds encoded compatible-version list for a given original version into caller buffer (with size negotiation). |
| `QUIC_STATUS` | `QuicVersionNegotiationExtParseVersionInfo` | Parses/validates Version Information transport data into structured chosen + available version fields. |
| `const uint8_t*` | `QuicVersionNegotiationExtEncodeVersionInfo` | Allocates and encodes Version Information blob for client/server use in version negotiation extension. |

# QUIC worker
| Return Type | Function Name | Description |
|---|---|---|
| `BOOLEAN` | `QuicWorkerIsOverloaded` | Returns whether a worker’s average queue delay exceeds configured overload threshold. |
| `void` | `QuicWorkerThreadWake` | Wakes/schedules a worker execution context (external pool or internal thread). |
| `QUIC_STATUS` | `QuicWorkerInitialize` | Initializes a single worker (locks/events/queues/timer wheel/execution context/thread). |
| `void` | `QuicWorkerUninitialize` | Stops and cleans up a single worker and its resources. |
| `void` | `QuicWorkerAssignConnection` | Assigns a connection to a worker/partition. |
| `void` | `QuicWorkerAssignListener` | Assigns a listener to a worker. |
| `BOOLEAN` | `QuicWorkerIsIdle` | Internal helper that checks if worker queues are empty. |
| `void` | `QuicWorkerQueueConnection` | Queues connection work on worker and wakes worker if needed. |
| `void` | `QuicWorkerQueuePriorityConnection` | Queues/moves a connection into priority portion of worker queue. |
| `void` | `QuicWorkerMoveConnection` | Internal helper to requeue connection onto another worker. |
| `void` | `QuicWorkerQueueListener` | Queues listener work on worker and wakes worker if needed. |
| `void` | `QuicWorkerQueueOperation` | Queues stateless operation on worker (or drops it if worker op limit reached). |
| `void` | `QuicWorkerUpdateQueueDelay` | Updates worker average queue delay using smoothing. |
| `void` | `QuicWorkerResetQueueDelay` | Resets worker queue-delay metric when becoming idle. |
| `QUIC_CONNECTION*` | `QuicWorkerGetNextConnection` | Internal helper that dequeues next connection work item. |
| `QUIC_LISTENER*` | `QuicWorkerGetNextListener` | Internal helper that dequeues next listener work item. |
| `QUIC_OPERATION*` | `QuicWorkerGetNextOperation` | Internal helper that dequeues next stateless operation. |
| `void` | `QuicWorkerProcessTimers` | Processes expired connection timers from worker timer wheel. |
| `void` | `QuicWorkerProcessConnection` | Processes one queued connection drain cycle and requeues/migrates as needed. |
| `void` | `QuicWorkerProcessListener` | Processes one queued listener drain cycle and requeues if needed. |
| `void` | `QuicWorkerLoopCleanup` | Cleans pending worker queues during shutdown and releases held refs. |
| `BOOLEAN` | `QuicWorkerLoop` | Executes one worker-loop iteration; processes timers/conn/listener/ops and decides next wake behavior. |
| `CXPLAT_THREAD_CALLBACK` | `QuicWorkerThread` | Worker thread entry that repeatedly runs `QuicWorkerLoop`. |
| `QUIC_STATUS` | `QuicWorkerPoolInitialize` | Allocates and initializes all workers for a registration. |
| `void` | `QuicWorkerPoolUninitialize` | Uninitializes all workers and frees worker pool memory. |
| `BOOLEAN` | `QuicWorkerPoolIsOverloaded` | Returns whether every worker in the pool is currently overloaded. |
| `uint16_t` | `QuicWorkerPoolGetLeastLoadedWorker` | Chooses worker index with lowest average queue delay (with rotation bias). |
| `BOOLEAN` | `QuicWorkerPoolIsInPartition` | Checks whether current thread is executing in a given partition’s worker context. |
