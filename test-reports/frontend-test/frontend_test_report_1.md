
> frontend@0.0.0 test
> vitest run --reporter=verbose --no-color


 RUN  v5.1.0 /home/dell/Sun_Development/projects/Personal_Knowledge_Base/frontend

 ✓ src/services/__tests__/ragService.test.js > isIndexed > returns true for uppercase INDEXED 5ms
 ✓ src/services/__tests__/ragService.test.js > isIndexed > handles lowercase status casing 1ms
 ✓ src/services/__tests__/ragService.test.js > isIndexed > handles snake_case indexing_status field 1ms
 ✓ src/services/__tests__/ragService.test.js > isIndexed > returns false for non-INDEXED statuses and missing values 1ms
 ✓ src/services/__tests__/ragService.test.js > selectableDocuments > filters out non-INDEXED documents 1ms
 ✓ src/services/__tests__/ragService.test.js > selectableDocuments > returns an empty array when nothing is indexed 1ms
 ✓ src/services/__tests__/ragService.test.js > addToSelection > appends a new document to the selection 1ms
 ✓ src/services/__tests__/ragService.test.js > addToSelection > returns the same reference (unchanged) for a duplicate 1ms
 ✓ src/services/__tests__/ragService.test.js > addToSelection > throws when a 6th document is added 2ms
 ✓ src/services/__tests__/ragService.test.js > addToSelection > allows exactly MAX_SELECTED_FILES 0ms
 ✓ src/services/__tests__/ragService.test.js > removeFromSelection > removes a document by fileId and keeps order 0ms
 ✓ src/services/__tests__/ragService.test.js > removeFromSelection > is a no-op (new array) when the fileId is absent 1ms
 ✓ src/services/__tests__/ragService.test.js > toFileIds > maps selection objects to a numeric file_ids array 0ms
 ✓ src/services/__tests__/ragService.test.js > toFileIds > returns an empty array for no selection 0ms
 ✓ src/services/__tests__/ragService.test.js > formatSelectionLabel > prefers title over filename 0ms
 ✓ src/services/__tests__/ragService.test.js > formatSelectionLabel > falls back to filename when title is empty 0ms
 ✓ src/services/__tests__/ragHistoryService.test.js > saveSession / loadSession round-trip > persists messages and selected docs and restores them intact 10ms
 ✓ src/services/__tests__/ragHistoryService.test.js > saveSession / loadSession round-trip > scopes storage per user (no cross-user leakage) 1ms
 ✓ src/services/__tests__/ragHistoryService.test.js > normalization on restore > forces streaming to false on restored messages 0ms
 ✓ src/services/__tests__/ragHistoryService.test.js > normalization on restore > drops malformed message entries instead of trusting them 1ms
 ✓ src/services/__tests__/ragHistoryService.test.js > normalization on restore > caps the saved messages to the MAX limit 1ms
 ✓ src/services/__tests__/ragHistoryService.test.js > robustness > returns an empty session when nothing is stored 0ms
 ✓ src/services/__tests__/ragHistoryService.test.js > robustness > degrades gracefully on corrupt stored JSON 13ms
 ✓ src/services/__tests__/ragHistoryService.test.js > robustness > does not throw when storage itself is unavailable 5ms
 ✓ src/services/__tests__/ragHistoryService.test.js > robustness > clears only the target user session 4ms
 ✓ src/services/__tests__/vaultSelectionService.test.js > partitionForKnowledgeBase > splits indexed vs non-indexed 6ms
 ✓ src/services/__tests__/vaultSelectionService.test.js > partitionForKnowledgeBase > handles empty and null inputs 2ms
 ✓ src/services/__tests__/vaultSelectionService.test.js > partitionForKnowledgeBase > is case-insensitive via isIndexed (lowercase indexed) 2ms
 ✓ src/apis/__tests__/ragApi.test.js > parseSseEvents > extracts only objects from data: lines 4ms
 ✓ src/apis/__tests__/ragApi.test.js > parseSseEvents > ignores non-data lines (comments) and blank lines 1ms
 ✓ src/apis/__tests__/ragApi.test.js > parseSseEvents > skips malformed JSON lines without throwing 0ms
 ✓ src/apis/__tests__/ragApi.test.js > parseSseEvents > tolerates CRLF line endings 0ms
 ✓ src/apis/__tests__/ragApi.test.js > parseRagSse > accumulates the full answer from token-only events 1ms
 ✓ src/apis/__tests__/ragApi.test.js > parseRagSse > enriches with sources and diagnostics from the final event 1ms
 ✓ src/apis/__tests__/ragApi.test.js > parseRagSse > yields [] for a final event with empty sources 0ms
 ✓ src/apis/__tests__/ragApi.test.js > parseRagSse > ignores comment lines interspersed among data lines 0ms
 ✓ src/apis/__tests__/ragApi.test.js > parseRagSse > skips malformed lines without throwing or losing subsequent tokens 1ms
 ✓ src/apis/__tests__/ragApi.test.js > parseRagSse > captures a mid-stream error event surfaced by the backend 0ms
 ✓ src/apis/__tests__/ragApi.test.js > parseRagSse > returns an empty answer object for an empty body 0ms

 Test Files  4 passed (4)
      Tests  39 passed (39)
   Start at  13:47:45
   Duration  603ms (import 54%, tests 20%, transform 14%, worker 11%)

