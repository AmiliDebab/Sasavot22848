def test_security_policies():
    queues = QueuesDirectory()
    monitor = SecurityMonitor(queues)
    
    # Тест разрешённого события
    valid_event = Event(
        source="control",
        destination="safety",
        operation="set_speed",
        parameters=50
    )
    assert monitor._check_event(valid_event) == True
    
    # Тест запрещённого события
    invalid_event = Event(
        source="navigation",
        destination="servos",
        operation="set_direction",
        parameters=90
    )
    assert monitor._check_event(invalid_event) == False

#### 5. Запуск полной системы
python
# Инициализация
queues = QueuesDirectory()
components = [
    MissionPlanner(queues),
    CommunicationGateway(queues),
    ControlSystem(queues),
    NavigationSystem(queues),
    Servos(queues),
    CargoBay(queues),
    SafetyBlock(queues),
    SecurityMonitor(queues)  # Добавляем монитор
]

# Запуск
for c in components:
    c.start()

# Активация киберпрепятствий
control_system = next(c for c in components if isinstance(c, ControlSystem))
control_system.enable_surprises()

# Ожидание
sleep(300)

# Остановка
for c in components:
    c.stop()
    c.join()
`

---

### Критерии полного выполнения:
1. Для части 3.1:
   - Реализован SafetyBlock с контролем:
     - Скорости
     - Направления
     - Позиции на маршруте
     - Времени ответа системы управления
     - Точки выгрузки груза

2. Для части 3.2:
   - Реализован SecurityMonitor с политиками для всех разрешённых взаимодействий
   - Все компоненты модифицированы для работы через монитор
   - Есть тесты для проверки политик

3. Дополнительно:
   - Система устойчива к киберпрепятствиям
   - Обеспечены ЦБ1 и ЦБ3
   - Логирование всех критических событий

Это полное решение соответствует всем требованиям модуля 3. Для выполнения задания повышенной сложности (ЦБ2) потребуется дополнительная реализация цифровой подписи маршрутов.
