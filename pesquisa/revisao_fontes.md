# Coleta para a revisão técnica — 10/09/2026

Fontes primárias consultadas pela IA nesta sessão. Manuais históricos hospedados por terceiros são identificados como cópias; não se confundem com resenhas.

| Assunto | Dado a transcrever / decisão técnica | Fonte | Acesso |
|---|---|---|---|
| SAS-4 | 22,5 Gb/s; 128b/150b; cálculo: 2.400 MB/s por direção, 4.800 MB/s agregado, antes de overhead adicional | https://www.microchip.com/en-us/solutions/data-centers-and-computing/data-center-solutions/technologies/24g-sas-technology | 10/09/2026 |
| SATA | 1,5 / 3 / 6 Gb/s; nomenclatura por taxa | https://sata-io.org/developers/sata-naming-guidelines | 10/09/2026 |
| eSATA | Especificações externas publicadas em 2004; 2 m; requisitos elétricos próprios | https://sata-io.org/developers/sata-ecosystem/esata?language=en | 10/09/2026 |
| USB 3.2 | Gen 1: 5; Gen 2: 10; Gen 2x2: 20 Gb/s | https://www.usb.org/usb-32 | 10/09/2026 |
| USB4 | 80 Gb/s; opção assimétrica 120/40 Gb/s; PAM3 | https://www.usb.org/sites/default/files/2022-10/USB-IF%20USB%2080Gbps%20Announcement_FINAL_v2.pdf | 10/09/2026 |
| Energia USB | USB Power Delivery é especificação distinta | https://www.usb.org/usb-charger-pd | 10/09/2026 |
| FireWire | Implementação TI com taxas 100/200/400/800 Mb/s | https://www.ti.com/product/TSB83AA23/part-details/TSB83AA23ZAY | 10/09/2026 |
| RAID 1.5 | Manual HighPoint HPT370/372N rev. 1.0, fev. 2003: modo mirror, duplicação e reconstrução; DFI descreve dois discos | https://doczz.net/doc/5385162/hpt370-372-raid-controller ; https://www.manuallib.com/download/pdf8/REV.-AA--SYSTEM-BOARD-USER-MANUAL.PDF | 10/09/2026 |
| RAID 7 | Relato primário da Storage Computer: controladores, memória distribuída/centralizada e SO de tempo real | https://www.sec.gov/Archives/edgar/data/933452/000092701603001556/d10k.htm | 10/09/2026 |
| RAID-DP | Dupla paridade; fundamento RDP, XOR por linhas e diagonais | https://www.usenix.org/conference/fast-04/row-diagonal-parity-double-disk-failure-correction ; https://docs.netapp.com/us-en/ontap/concepts/aggregates-raid-groups-concept.html | 10/09/2026 |
| RAID-S | Brett Quinn, EMC: grupos 3+1 (75%); 7+1 em EOS (87,5%); volumes sem striping tradicional, XOR nos discos, HVE | https://ntrs.nasa.gov/api/citations/19960052763/downloads/19960052763.pdf | 10/09/2026 |
| Matrix | Até dois volumes sobre um conjunto físico | https://www.intel.com/content/www/us/en/support/articles/000006406/technologies.html | 10/09/2026 |
| MTTF/MTBF | Modelo exponencial e tempo de operação | https://www.itl.nist.gov/div898/handbook/apr/section3/apr311.htm | 10/09/2026 |
| WAL | Commit síncrono configurável; group commit; fsync | https://www.postgresql.org/docs/16/runtime-config-wal.html | 10/09/2026 |
| Penalidade | Pequena escrita RAID 5: 4 I/Os em RMW; full stripe é diferente | https://www.usenix.org/publications/library/proceedings/usenix2000/freenix/full_papers/gopinath/gopinath_html/node24.html | 10/09/2026 |
| Integridade | Write hole; limites de paridade e scrubbing | https://spdk.io/news/2024/02/12/raid5f/ ; https://www.usenix.org/legacy/event/fast08/tech/full_papers/krioukov/krioukov_html/main.html | 10/09/2026 |

## Cálculos próprios para os exemplos

Premissas didáticas, sem medição de hardware: 8 discos de 4 TB, capacidades 32 TB (RAID 0), 16 TB (10), 28 TB (5), 24 TB (6). Para 150 IOPS/disco e 70% leituras, custos por escrita 2/4/6: 1200/(0,7+0,3p), respectivamente 923/632/480 IOPS lógicos aproximados.

Modelo de falhas: M=1.200.000 h, R=24 h. RAID 1: M²/(2R)=30.000.000.000 h; RAID 5 com 8 discos: M²/(8·7·R)=1.071.428.571 h. Para RAID 6, usar apenas um modelo de Markov declarado (reparo serial de taxa 1/R), derivando M³/[n(n−1)(n−2)R²] como termo dominante; não aplicar a reparos paralelos ou determinísticos.

## Complementos verificados

- USB4 20/40 Gb/s: USB-IF System Overview, https://www.usb.org/sites/default/files/D1T1-3%20-%20USB4%20System%20Overview.pdf (10/09/2026).
- Fila SAS de 128 comandos é exemplo do Barracuda ES.2, não regra do protocolo: manual Seagate 100496298G, chave `seagate_sas` (10/09/2026).
- AHCI: Intel revisão 1.3.1, chave `intel_ahci`; interface de programação e até 32 slots (10/09/2026).
- Contagem didática de Hamming: 2^r >= k+r+1; exemplo k=4, r=3. Derivação de síndromes, não especificação de um produto.
