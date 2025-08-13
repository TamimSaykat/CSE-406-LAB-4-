// 2_sendSingle_target.ino
#include <painlessMesh.h>
#define   MESH_PREFIX     "MeshLabDemo"
#define   MESH_PASSWORD   "mesh_password123"
#define   MESH_PORT       5555
Scheduler userScheduler; painlessMesh mesh;
uint32_t targetId = 0;
Task taskAnnounce( TASK_SECOND * 7, TASK_FOREVER, [](){ String msg = String("Hello (broadcast) from ")+mesh.getNodeId(); mesh.sendBroadcast(msg);} );
void receivedCallback(uint32_t from, String &msg){ Serial.printf("[RX] from=%u msg=%s\n", from, msg.c_str()); }
void newConnectionCallback(uint32_t nodeId){ Serial.printf("--> New Connection, nodeId = %u\n", nodeId); }
void changedConnectionCallback(){ Serial.printf("Changed connections\n"); }
void nodeTimeAdjustedCallback(int32_t offset){ Serial.printf("Adjusted time %u. Offset = %d\n",(unsigned)mesh.getNodeTime(), offset); }
bool isConnected(uint32_t id){ auto nodes=mesh.getNodeList(true); for(auto &&n:nodes) if(n==id) return true; return false; }
void trySendDirect(const String& msg){ if(targetId==0){Serial.println("[WARN] Target not set. Use T:<id>"); return;}
  if(!isConnected(targetId)){ Serial.printf("[WARN] Target %u not connected.\n", targetId); return;}
  bool ok = mesh.sendSingle(targetId, msg); Serial.printf("[TX] to %u ok=%s msg=%s\n", targetId, ok?"true":"false", msg.c_str()); }
void handleSerial(){ if(!Serial.available()) return; String line=Serial.readStringUntil('\n'); line.trim();
  if(line.startsWith("T:")){ targetId=(uint32_t)strtoul(line.substring(2).c_str(), nullptr, 10); Serial.printf("[INFO] Target set to %u\n", targetId); }
  else if(line.startsWith("M:")){ trySendDirect(line.substring(2)); }
  else if(line=="?"){ Serial.printf("[INFO] My nodeId=%u\n", mesh.getNodeId()); Serial.print("[INFO] Neighbors:"); auto nodes=mesh.getNodeList(true); for(auto&&n:nodes){Serial.print(" "); Serial.print(n);} Serial.println(); }
  else { Serial.println("[HELP] Use T:<id>, M:<text>, or ?"); } }
void setup(){ Serial.begin(115200); delay(200); mesh.setDebugMsgTypes(ERROR|STARTUP|CONNECTION);
  mesh.init(MESH_PREFIX, MESH_PASSWORD, &userScheduler, MESH_PORT);
  mesh.onReceive(&receivedCallback); mesh.onNewConnection(&newConnectionCallback);
  mesh.onChangedConnections(&changedConnectionCallback); mesh.onNodeTimeAdjusted(&nodeTimeAdjustedCallback);
  userScheduler.addTask(taskAnnounce); taskAnnounce.enable(); }
void loop(){ mesh.update(); handleSerial(); }
