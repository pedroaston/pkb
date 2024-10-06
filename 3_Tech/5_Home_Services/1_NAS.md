
# Decentralized storages

Networks:
- Filecoin
	- Docs: https://docs.filecoin.io

## Storage Market

Price of TiB per month for personal/business basic plans. More advanced plans are more expensive

| Year\Provider | Google | Microsoft |
| ------------- | ------ | --------- |
| 09/2024       | 4.10$  | 5$        |

## DIY Cost

- Disk

# Personal NAS

## Required Specs

- Network: dual 10GbE
- Form factor: rack-server with 1 or 2 RU
- Storage Driver: SSD for lower noise. Still analyzing if I should chose SATA or NVMe

## Storage specs to check

Here are the most relevant to use attributes when analyzing SSD acquisitions

- Longevity:
	- DWPD (Drive Writes Per Day)
	- TBW (means the amount of Terabits writes warranted for the drive life)
	- DWPD = TWD/((Drive Capacity * 365 * (warranty in years)))
- Performance:
	- IOPS
- Capacity
	- Terabits

NVMes List:

| Model Name          | Storage (TB) | Read IOPS | Write IOPS | PCIe Gen | Sequencial Read Speed (Mb/s) | Sequencial Write Speed (Mb/s) | TBW  | Power Idle (mW) | Power Work (avg/max W) | Warranty (years) | DRAM |
| ------------------- | ------------ | --------- | ---------- | -------- | ---------------------------- | ----------------------------- | ---- | --------------- | ---------------------- | ---------------- | ---- |
| Crucial P3 Plus 4TB | 4            | 650k      | 900k       | 4.0      | 5000                         |                               |      |                 |                        |                  |      |
| Samsung 990 Pro SSD | 4            | 1600k     | 80         | 4.0      | 7450                         | 6900                          | 2400 | 55              | 6.5/8.6                | 5                |      |
| WD_Black SN850X     | 4            |           |            | 4.0      | 7300                         |                               |      |                 |                        |                  |      |
| Kingston NV2        | 4            |           |            | 4.0      | 3500                         |                               |      |                 |                        |                  |      |
| Lexar NM790         | 4            |           |            | 4.0      | 7400                         | 6500                          | 3000 |                 |                        | 5                | No   |
| Leven JPS800        | 4            |           |            | 4.0      | 5000                         | 4400                          |      |                 |                        |                  |      |
| FX991               | 4            |           |            | 4.0      | 7300                         | 6200                          | 2400 |                 |                        |                  |      |


## Architecture types

- NVMe NAS
	- Pros:
		- Fastest solution
		- Smaller form factor
	- Cons:
		- Requires a powerful CPU and expensive motherboard 
- SSD SATA NAS:
	- Pros:
		- Cheaper server requirements
	- Cons:
		- The same price of NVMes but slower
- HDD NAS
	- Pros:
		- Cheaper storage (over 3 times)
		- Cheaper server requirements
	- Cons:
		- Noisy for home environment