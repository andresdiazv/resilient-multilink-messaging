# resilient-multilink-messaging

## Roadmap

- [ ] **Phase 0: Environment and build**
  - Goal: CMake project with a library target, an executable, and a GoogleTest suite.
  - Done when: `cmake -B build && cmake --build build && ctest --test-dir build` passes from a clean clone.
- [ ] **Phase 1: Message format and serialization**
  - Goal: binary message format (magic, version, type, priority, sequence number, source ID, payload length, payload, CRC32) with explicit network-byte-order encode/decode that safely rejects malformed input.
  - Done when: round-trip and failure-case tests pass and the decoder survives 10,000 random buffers without crashing.
- [ ] **Phase 2: UDP link layer**
  - Goal: an abstract link interface with a UDP implementation, an RAII socket wrapper, and send/receive CLI tools.
  - Done when: messages flow between two terminals, the sender survives a receiver restart, and valgrind reports no leaks.
- [ ] **Phase 3: Reliable delivery (ARQ)**
  - Goal: sequence numbers, ACKs, timeout-based retransmission, duplicate suppression, and a sliding window, tested with a seeded lossy-link simulator and an injectable clock.
  - Done when: 10,000 messages sent through a 30% loss, 10% duplication link are each delivered exactly once.
- [ ] **Phase 4: Multi-link failover**
  - Goal: heartbeat-based link health state machine (UP, DEGRADED, DOWN), automatic failover with retransmission of unACKed messages, failback with hysteresis, and cross-link deduplication.
  - Done when: killing the primary link mid-stream loses no messages, and failback happens only after 5 seconds of stable health.
- [ ] **Phase 5: Priority queuing and rate limiting**
  - Goal: URGENT/NORMAL/BULK queues with a starvation guard and a token-bucket rate-limited link.
  - Done when: an URGENT message injected behind a 500-message BULK backlog is sent first.
- [ ] **Phase 6: Real network impairment testing**
  - Goal: a Linux network-namespace testbed with veth pairs and tc netem to apply real loss and delay, and to cut a link mid-stream.
  - Done when: one command runs the scenario and prints PASS or FAIL.
- [ ] **Phase 7: Hardening**
  - Goal: AddressSanitizer, UndefinedBehaviorSanitizer, and ThreadSanitizer runs, libFuzzer fuzzing of the decoder, and valgrind on the integration scenario.
  - Done when: sanitizers are clean and the fuzzer runs 30 minutes with no findings.
- [ ] **Phase 8: CI pipeline**
  - Goal: GitLab CI with build, test, sanitizer, and static-analysis (clang-tidy, cppcheck) stages.
  - Done when: a push produces a green pipeline.
- [ ] **Phase 9 (stretch): Event loop and concurrency**
  - Goal: epoll/timerfd event loop, optional thread-per-link design, optional libsodium encryption and SQLite delivery logging.
  - Done when: the full test suite and ThreadSanitizer pass with the new design.
