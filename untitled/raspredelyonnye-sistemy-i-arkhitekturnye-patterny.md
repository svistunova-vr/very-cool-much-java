# Распределённые системы и архитектурные паттерны

<details>

<summary>Вопросы</summary>

1. Проблемы распределённых систем и причины каскадных отказов
2. Partial failures: почему частичные отказы опаснее полных
3. Circuit Breaker (Resilience4j): принципы работы, состояния, настройки
4. Ограничения Circuit Breaker: зависания и необходимость TimeLimiter
5. Bulkhead Pattern: изоляция ресурсов и пулов потоков
6. Стратегии Fallback и Graceful Degradation
7. Retry Pattern: когда помогает и когда приводит к retry storm
8. Exponential backoff и jitter: зачем они нужны
9. Комбинация Retry, Circuit Breaker, Timeout и Bulkhead
10. Rate Limiting как защита системы, а не способ масштабирования
11. Saga Pattern: orchestration vs choreography, критерии выбора
12. Компенсирующие транзакции и отличие от rollback
13. Идемпотентность в распределённых бизнес-процессах
14. Idempotency keys: где хранить и как управлять жизненным циклом
15. Распределённые транзакции XA и причины отказа от них
16. Eventual Consistency и её влияние на бизнес-логику
17. Exactly-once delivery как миф распределённых систем
18. CQRS: плюсы, минусы и сценарии применения
19. Event Sourcing: хранение событий, snapshots, versioning
20. Совместное применение CQRS и Event Sourcing
21. Версионирование событий и схем данных
22. Синхронные vs асинхронные коммуникации: компромиссы
23. REST vs gRPC: критерии выбора
24. Message-driven архитектура (Kafka, RabbitMQ): когда оправдана
25. Dead Letter Queue и обработка poison messages
26. API Gateway: функционал, ограничения, cross-cutting concerns
27. BFF (Backend for Frontend): когда и зачем нужен
28. Sidecar Pattern и управление сервисными зависимостями
29. Strangler Fig Pattern: эволюционная миграция монолита
30. Observability: логи, метрики, трассировка
31. Distributed Tracing, correlation IDs и OpenTelemetry
32. Golden Signals и мониторинг продакшена
33. Реактивное программирование: Non-blocking I/O и Backpressure
34. Spring WebFlux vs Spring MVC
35. I/O-bound vs CPU-bound нагрузки
36. Масштабирование систем: Horizontal vs Vertical
37. Масштабирование по осям X, Y, Z
38. Монолит vs микросервисы: критерии выбора
39. Distributed Monolith: признаки и последствия
40. Общие базы данных между сервисами: риски и антипаттерны
41. Latency, network failures и CAP-теорема
42. Load Balancing: алгоритмы и стратегии
43. Distributed Cache: консистентность, репликация, TTL
44. Chaos Engineering и проверка устойчивости системы

</details>
