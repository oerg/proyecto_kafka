INFORME LABORATORIO 09



SALIDA DE TERMINAL

(.venv) estudiante@ip-192-168-166-59:/home/est1/equipo08/entorno$ docker compose -f docker-compose.etapaC.yml up -d
WARN[0000] Found orphan containers (kafka-ui, bootstrap-topics, broker2, broker1, broker3) for this project. If you removed or renamed this service in your compose file, you can run this command with the --remove-orphans flag to clean it up. 
[+] up 3/3
 ✔ Container connect         Running                                                                              0.0s
 ✔ Container postgres        Running                                                                              0.0s
 ✔ Container schema-registry Running                                                                              0.0s
(.venv) estudiante@ip-192-168-166-59:/home/est1/equipo08/entorno$ docker ps
CONTAINER ID   IMAGE                                   COMMAND                  CREATED             STATUS                   PORTS                                                                                                NAMES
d360f4f17162   postgres:16                             "docker-entrypoint.s…"   About an hour ago   Up About an hour         0.0.0.0:5433->5432/tcp, [::]:5433->5432/tcp                                                          postgres
0c30ccf0942d   dian-kafka-connect:1.0.0                "/etc/confluent/dock…"   5 days ago          Up 2 hours (unhealthy)   0.0.0.0:8083->8083/tcp, [::]:8083->8083/tcp, 9092/tcp                                                connect
d1ee390e6174   confluentinc/cp-schema-registry:7.7.0   "/etc/confluent/dock…"   7 days ago          Up 2 hours               0.0.0.0:8081->8081/tcp, [::]:8081->8081/tcp                                                          schema-registry
f25215d2ad67   ghcr.io/kafbat/kafka-ui:v1.1.0          "/bin/sh -c 'java --…"   7 days ago          Up 2 hours               0.0.0.0:8085->8080/tcp, [::]:8085->8080/tcp                                                          kafka-ui
1b0ee4bd912a   apache/kafka:3.9.0                      "/__cacert_entrypoin…"   6 weeks ago         Up 2 hours (healthy)     0.0.0.0:9094->9094/tcp, [::]:9094->9094/tcp, 9092/tcp, 0.0.0.0:9102->9102/tcp, [::]:9102->9102/tcp   broker2
56c9dae195d0   apache/kafka:3.9.0                      "/__cacert_entrypoin…"   6 weeks ago         Up 2 hours (healthy)     0.0.0.0:9092->9092/tcp, [::]:9092->9092/tcp, 0.0.0.0:9101->9101/tcp, [::]:9101->9101/tcp             broker1
8f002e6774a6   apache/kafka:3.9.0                      "/__cacert_entrypoin…"   6 weeks ago         Up 2 hours (healthy)     0.0.0.0:9096->9096/tcp, [::]:9096->9096/tcp, 9092/tcp, 0.0.0.0:9103->9103/tcp, [::]:9103->9103/tcp   broker3
(.venv) estudiante@ip-192-168-166-59:/home/est1/equipo08/entorno$ curl -s -X POST -H "Content-Type: application/json" --data @conector-debezium-source.json localhost:8083/connectors
(.venv) estudiante@ip-192-168-166-59:/home/est1/equipo08/entorno$ docker exec -it postgres psql -U dian -c "UPDATE declaraciones SET estado='CORREGIDA', actualizado_en=now() WHERE id_declaracion = 3;"
UPDATE 1
(.venv) estudiante@ip-192-168-166-59:/home/est1/equipo08/entorno$ docker exec -it postgres psql -U dian -c "DELETE FROM declaraciones WHERE id_declaracion = 4;"
DELETE 0
(.venv) estudiante@ip-192-168-166-59:/home/est1/equipo08/entorno$ docker exec broker1 /opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server broker1:19092 \
  --topic cdc.public.declaraciones --from-beginning --timeout-ms 15000 --property print.key=true 2>/dev/null | tail -5
(.venv) estudiante@ip-192-168-166-59:/home/est1/equipo08/entorno$ docker exec -it postgres psql -U dian -c "SELECT slot_name, active FROM pg_replication_slots;"
 slot_name  | active 
------------+--------
 dian_lab09 | t
(1 row)

(.venv) estudiante@ip-192-168-166-59:/home/est1/equipo08/entorno$ curl -s localhost:8083/connectors/declaraciones-s3-sink/status | python3 -m json.tool | grep -A2 tasks
    "tasks": [
        {
            "id": 0,
(.venv) estudiante@ip-192-168-166-59:/home/est1/equipo08/entorno$ docker restart connect && sleep 40
connect
(.venv) estudiante@ip-192-168-166-59:/home/est1/equipo08/entorno$ curl -s localhost:8083/connectors | python3 -m json.tool
[
    "declaraciones-s3-sink",
    "declaraciones-cdc-source",
    "declaraciones-jdbc-source"
]
(.venv) estudiante@ip-192-168-166-59:/home/est1/equipo08/entorno$ 