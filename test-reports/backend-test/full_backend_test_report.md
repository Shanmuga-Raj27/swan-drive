============================= test session starts ==============================
platform linux -- Python 3.12.15, pytest-9.1.1, pluggy-1.6.0
rootdir: /home/dell/Sun_Development/projects/Personal_Knowledge_Base/backend
configfile: pytest.ini
testpaths: tests
plugins: anyio-4.14.2
collected 417 items

tests/integration/routes/test_pagination.py .....                        [  1%]
tests/integration/routes/test_rag_document_routes.py .............       [  4%]
tests/integration/routes/test_rag_metrics.py ....                        [  5%]
tests/integration/routes/test_rag_routes.py .....                        [  6%]
tests/integration/routes/test_semantic_search.py ......                  [  7%]
tests/integration/routes/test_system.py .                                [  8%]
tests/integration/routes/test_upload_file.py ............                [ 11%]
tests/integration/scenarios/test_outage_behavior.py ...                  [ 11%]
tests/integration/scenarios/test_partial_write_retry.py ..               [ 12%]
tests/integration/scenarios/test_reindex_during_query.py .               [ 12%]
tests/integration/scenarios/test_revocation.py ...                       [ 13%]
tests/integration/security/test_auth_no_token.py ......                  [ 14%]
tests/integration/security/test_cache_isolation.py .....                 [ 15%]
tests/integration/security/test_guessed_ids.py ......                    [ 17%]
tests/integration/security/test_payload_tampering.py .........           [ 19%]
tests/integration/security/test_tenant_isolation.py ........             [ 21%]
tests/unit/core/test_config.py .....                                     [ 22%]
tests/unit/services/rag/test_answer_cache.py ........                    [ 24%]
tests/unit/services/rag/test_chunker.py .....................            [ 29%]
tests/unit/services/rag/test_document_processor.py .........             [ 31%]
tests/unit/services/rag/test_embedding.py ............                   [ 34%]
tests/unit/services/rag/test_generation.py ...............               [ 38%]
tests/unit/services/rag/test_ids.py .........                            [ 40%]
tests/unit/services/rag/test_indexing_service.py ..........              [ 42%]
tests/unit/services/rag/test_inspection.py ..............                [ 46%]
tests/unit/services/rag/test_metrics.py ..............................   [ 53%]
tests/unit/services/rag/test_orchestrator.py ........                    [ 55%]
tests/unit/services/rag/test_orchestrator_metrics.py ......              [ 56%]
tests/unit/services/rag/test_pdf_extractor.py .....                      [ 57%]
tests/unit/services/rag/test_persistence.py ............................ [ 64%]
..                                                                       [ 64%]
tests/unit/services/rag/test_phase4_integration.py ...                   [ 65%]
tests/unit/services/rag/test_qdrant_service.py .....................     [ 70%]
tests/unit/services/rag/test_query_service.py .............              [ 73%]
tests/unit/services/rag/test_query_utils.py ............................ [ 80%]
..                                                                       [ 81%]
tests/unit/services/rag/test_retry.py .........................          [ 87%]
tests/unit/services/rag/test_runtime_metrics.py ..........               [ 89%]
tests/unit/services/rag/test_schemas.py ...                              [ 90%]
tests/unit/services/rag/test_text_cleaner.py .......                     [ 91%]
tests/unit/services/test_rag_vector_service.py ....                      [ 92%]
tests/unit/services/test_redis_cache.py ......                           [ 94%]
tests/unit/services/test_s3_service.py ................                  [ 98%]
tests/unit/services/test_vector_service.py ........                      [100%]

============================= 417 passed in 7.27s ==============================
