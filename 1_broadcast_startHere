// 1_broadcast_startHere.ino
#include <painlessMesh.h>
#include <ESPAsyncTCP.h>
#define   MESH_PREFIX     "MeshLabDemo"
#define   MESH_PASSWORD   "mesh_password123"
#define   MESH_PORT       5555
Scheduler userScheduler; painlessMesh mesh;
Task taskSendMessage( TASK_SECOND * 1, TASK_FOREVER, []() {
String msg = String("Hello from node ") + mesh.getNodeId();
mesh.sendBroadcast(msg);
Serial.printf("[TX] %s\n", msg.c_str());
taskSendMessage.setInterval( random(TASK_SECOND * 1, TASK_SECOND * 5) );
});
void receivedCallback(uint32_t from, String &msg){ Serial.printf("[RX] from=%u msg=%s\n", from, msg.c_str()); }
void newConnectionCallback(uint32_t nodeId){ Serial.printf("--> startHere: New Connection, nodeId = %u\n", nodeId); }
void changedConnectionCallback(){ Serial.printf("Changed connections\n"); }
void nodeTimeAdjustedCallback(int32_t offset){ Serial.printf("Adjusted time %u. Offset = %d\n",(unsigned)mesh.getNodeTime(), offset); }
void setup(){ Serial.begin(115200); delay(200); mesh.setDebugMsgTypes(ERROR|STARTUP|CONNECTION);
  mesh.init(MESH_PREFIX, MESH_PASSWORD, &userScheduler, MESH_PORT);
  mesh.onReceive(&receivedCallback); mesh.onNewConnection(&newConnectionCallback);
  mesh.onChangedConnections(&changedConnectionCallback); mesh.onNodeTimeAdjusted(&nodeTimeAdjustedCallback);
  userScheduler.addTask(taskSendMessage); taskSendMessage.enable(); }
void loop(){ mesh.update(); }
