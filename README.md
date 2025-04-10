from multiprocessing import Queue
from src.communication_gateway import BaseCommunicationGateway
from src.config import CONTROL_SYSTEM_QUEUE_NAME
from src.event_types import Event

class CommunicationGateway(BaseCommunicationGateway):
    def _send_mission_to_consumers(self):
        """ метод для отправки сообщения с маршрутным заданием в систему управления """
        control_q_name = CONTROL_SYSTEM_QUEUE_NAME
        event = Event(source=BaseCommunicationGateway.event_source_name,
                      destination=control_q_name,
                      operation="set_mission", parameters=self._mission
                      )
        control_q: Queue = self._queues_dir.get_queue(control_q_name)
        # отправка события в найденную очередь
        control_q.put(event)
from multiprocessing import Queue
from src.config import SERVOS_QUEUE_NAME, CARGO_BAY_QUEUE_NAME, CONTROL_SYSTEM_QUEUE_NAME
from src.event_types import Event
from src.control_system import BaseControlSystem

==========================================================================================

class ControlSystem(BaseControlSystem):
    def _send_speed_and_direction_to_consumers(self, speed, direction):
        servos_q_name = SERVOS_QUEUE_NAME
        servos_q = self._queues_dir.get_queue(servos_q_name)
        event_speed = Event(source=self.event_source_name,
                          destination=servos_q_name,
                          operation="set_speed",
                          parameters=speed)
        event_direction = Event(source=self.event_source_name,
                              destination=servos_q_name,
                              operation="set_direction",
                              parameters=direction)
        servos_q.put(event_speed)
        servos_q.put(event_direction)
    def _lock_cargo(self):
        cargo_q = self._queues_dir.get_queue(CARGO_BAY_QUEUE_NAME)
        event = Event(source=CONTROL_SYSTEM_QUEUE_NAME,
                     destination=CARGO_BAY_QUEUE_NAME,
                     operation="lock_cargo",
                     parameters=None)
        cargo_q.put(event)
    def _release_cargo(self):
        cargo_q = self._queues_dir.get_queue(CARGO_BAY_QUEUE_NAME)
        event = Event(source=CONTROL_SYSTEM_QUEUE_NAME,
                     destination=CARGO_BAY_QUEUE_NAME,
                     operation="release_cargo",
                     parameters=None)
        cargo_q.put(event)
  =============================================================================      
from multiprocessing import Queue
from src.config import CONTROL_SYSTEM_QUEUE_NAME
from src.event_types import Event
from src.navigation_system import BaseNavigationSystem

class NavigationSystem(BaseNavigationSystem):
    def _send_position_to_consumers(self):
        control_q_name = CONTROL_SYSTEM_QUEUE_NAME
        event = Event(source=self.event_source_name,
                     destination=control_q_name,
                     operation="position_update",
                     parameters=self._position)
        control_q = self._queues_dir.get_queue(control_q_name)
        control_q.put(event)

queues_dir = QueuesDirectory()
