## Paso 2: acks
| acks | ev/s |
|---|---|
| 0 | 10016 |
| 1 | 6454 |
| all | 1462 |

## Paso 3: linger.ms × compression.type (acks=all)
| linger.ms | compression | ev/s |
|---|---|---|
| 0 | none | 1543 |
| 0 | snappy | 1901 |
| 0 | zstd | 1912 |
| 20 | none | 20482 |
| 20 | snappy | 29750 |
| 20 | zstd | 29300 |
| 100 | none | 31447 |
| 100 | snappy | 32201 |
| 100 | zstd | 32636 |

Tamaño en disco por combinación: docker exec broker1 du -sh /var/lib/kafka/data/equipo08Jueves.lab05.facturacion.emitida-*
