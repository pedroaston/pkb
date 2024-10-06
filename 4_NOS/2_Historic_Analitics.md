
# Future ODS 5 min Table

Colunas:
- Timestamp (netmetrics.ports.timestamp)
- Device (netmetrics.ports.device.display)
- Interface (netmetrics.ports.tags.ifName)
- Sentido (uses inbound metrics)
- Localização (uses netmetrics.ports.device.display and correlates with what???)
- Serviço (uses netmetrics.ports.tags.ifName)
- Provider (uses netmetrics.ports.tags.ifName)
- Bytes (netmetrics.ports.fields.inoctets)
- Rate (netmetrics.ports.fields.ifInBits_rate)

# Metadata Table

Is this necessary? If so, what are the relevant attributes

Columns:
- Device
- Location

## Relevant Queries

### Query 1

SELECT device.display, tags.ifName, sum(fields.inoctets/(1024*1024)) as acum, count(fields.inoctets) as n_amostras FROM `networkanalytics-p-292818.netmetrix.ports`
WHERE day_part = "2024-09-24" and lower(device.sysName) like '%brip%'
Group by device.display, tags.ifName


### Query 2
SELECT distinct device.display FROM `networkanalytics-p-292818.netmetrix.ports` WHERE day_part = "2024-09-24" and lower(device.sysName) like '%erip%' LIMIT 1000

### Query join raw + location
---------------------------------------
SELECT raw.device.display, raw.tags.ifName, local.cidade, max(raw.fields.ifInBits_rate) as max_in
FROM `networkanalytics-p-292818.netmetrix.ports` raw
join `networkanalytics-dp-p-975413.netmetrix.interface_peering_cdn_trans` local on raw.device.display = local.node
WHERE day_part = "2024-09-24" and lower(device.sysName) like '%brip%'
Group by device.display, tags.ifName, local.cidade

---------------------------------------------
### Query - Proto future table v1
--------------------------------------
SELECT device.last_polled as data_time, device.display as router, tags.ifName as interface, "IN" as traffic_profile, regexp_substr(upper(device.display), '[A-Z]*') as location, regexp_substr(lower(tags.ifAlias), '.*s:([a-z]+) .*') as Service,

  regexp_substr(lower(tags.ifAlias), '([a-z]+):.*') as Provider, fields.ifInBits_rate as five_min_rate, fields.inoctets as bytes

FROM `networkanalytics-p-292818.netmetrix.ports`
WHERE day_part = "2024-09-24" and (lower(device.display) like '%brip%' or lower(device.display) like '[a-z]{5}[1-9]erip%')

--------------------------------------------------
Limitations:
- Transit interface descriptions do not have the same format as Peering. Same issue applies to IXP.
## Questions

- ports - location.id what is it? **(ONGOING)**
	- Response >> location.id connects to a new table. Checking with the source what is possible.
- Qual o pooling a ser usado no LibreNMS? Existem interfaces que têm 133 registos por dia e outros 112 ou entre estes dois valores. **(RESOLVED)**
	- Response >> Handled after 27/09. It was caused by an issue.
- Exporting yearly csv most be done via BigQuery **(TODO)** 

# Old ODS computed tables

- interface_peering_cdn_trans
	- Objective: Typifying the attributes Location, Service and Provider of all CDN and peering interfaces
	- Method: It used ca_pfm devices and interfaces tables where the hostname, interface_name and interface_alias where used to perform the classification. It uses static mapping to perform the interface classification.
	- Questions: 
		- Lifecycle state ??? Needed for what?
- traf_peering_cdn_trans_5m
	- Objective: Traffic rates and usage percentage per profile and interface of the interface_peering_cdn_trans table.
	- Method: One query per traffic profile, associate the interface metadata id with the relevant interface rate in mbps.
	- Questions: 
		- Do we use percentage of use, or can we ignore it moving forward?

### Query to update ods_metadata

Query:
```sql
select distinct * from (

select raw.device.sysName node_fullname

, upper(case

  when instr(raw.device.sysName, '.', 1) <> 0 then substr(raw.device.sysName, 0, instr(raw.device.sysName, '.', 1)-1)

  else raw.device.sysName

  end) node

, raw.tags.port_id interfaceid

, raw.tags.ifName interface

, raw.tags.ifAlias alias

, case

    when upper(raw.tags.ifAlias) like '%S:PEERING%' then 'Peering'

    when upper(raw.tags.ifAlias) like '%S:CDN%' then 'CDN'

    when upper(raw.tags.ifAlias) like '%S:TRANSITO%' then 'Trânsito'

    when upper(raw.tags.ifAlias) like '%S:IPX-GRX%' then 'IPX-GRX'

    else null

  end tipo

, case

    when upper(raw.device.sysName) like '%TLV1%' then 'Telvent'

    when upper(raw.device.sysName) like '%IMO1%' then 'Imopolis'

    when upper(raw.device.sysName) like '%PDG1%' then 'Ponta Delgada'

    when upper(raw.device.sysName) like '%TER1%' then 'Terceira'

    when upper(raw.device.sysName) like '%CMP1%' then 'Campanhã'

    when upper(raw.device.sysName) like '%PCE1%' then 'Porto Centro'

    when upper(raw.device.sysName) like '%ESMAD1%' or upper(raw.device.sysName) like '%NLSPL1%' then 'Interxion'

    when upper(raw.device.sysName) like '%GBLON1%' then 'Telehouse'

    when upper(raw.device.sysName) like '%FUN1%' then 'Funchal'

    when upper(raw.device.sysName) like '%ESMAD2%' then 'Equinix'

    else null

  end localizacao

, case

    when upper(raw.device.sysName) like '%TLV1%' then 'Lisboa'

    when upper(raw.device.sysName) like '%IMO1%' then 'Lisboa'

    when upper(raw.device.sysName) like '%PDG1%' then 'Ponta Delgada'

    when upper(raw.device.sysName) like '%TER1%' then 'Terceira'

    when upper(raw.device.sysName) like '%CMP1%' then 'Porto'

    when upper(raw.device.sysName) like '%PCE1%' then 'Porto'

    when upper(raw.device.sysName) like '%ESMAD%' then 'Madrid'

    when upper(raw.device.sysName) like '%GBLON1%' then 'Londres'

    when upper(raw.device.sysName) like '%FUN1%' then 'Funchal'

    when upper(raw.device.sysName) like '%NLSPL1%' then 'Amesterdão'

    else null

  end cidade

, case

    when upper(raw.tags.ifAlias) like '%S:PEERING%' then

      case

        when upper(raw.tags.ifAlias) like '%GOOGLE%' then 'Google'

        when upper(raw.tags.ifAlias) like '%GIGAPIX%' then 'Gigapix'

        when upper(raw.tags.ifAlias) like '%MICROSOFT%' then 'Microsoft'

        when upper(raw.tags.ifAlias) like '%MEO%' then 'Meo'

        when upper(raw.tags.ifAlias) like '%FACEBOOK%' then 'Facebook'

        when upper(raw.tags.ifAlias) like '%AMAZON%' then 'Amazon'

        when upper(raw.tags.ifAlias) like '%TWITCH%' then 'Twitch'

        when upper(raw.tags.ifAlias) like '%LIMELIGHT%' then 'Limelight'

        when upper(raw.tags.ifAlias) like '%ESPANIX%' then 'Espanix'

        when upper(raw.tags.ifAlias) like '%LINX%' then 'Linx'

        when upper(raw.tags.ifAlias) like '%CDN77%' then 'CDN77'

        when upper(raw.tags.ifAlias) like '%VALVE%' then 'Valve'

        when upper(raw.tags.ifAlias) like '%RETN%' then 'RETN'

        when upper(raw.tags.ifAlias) like '%VERIZON%' then 'Verizon'

        when upper(raw.tags.ifAlias) like '%NETFLIX%' then 'Netflix'

        when upper(raw.tags.ifAlias) like '%VODAFONE%' then 'Vodafone'

        when upper(raw.tags.ifAlias) like '%AKAMAI%' then 'Akamai'

        when upper(raw.tags.ifAlias) like '%CLOUDFLARE%' then 'Cloudflare'

        when upper(raw.tags.ifAlias) like '%APPLE%' then 'APPLE'

        when upper(raw.tags.ifAlias) like '%BLIZZARD%' then 'BLIZZARD'

        when upper(raw.tags.ifAlias) like '%3D-UBISOFT%' then 'i3D-UBISOFT'

        when upper(raw.tags.ifAlias) like '%VOLTERRA%' then 'VOLTERRA'

        when upper(raw.tags.ifAlias) like '%SUBSPACE:%' then 'SUBSPACE'

        when upper(raw.tags.ifAlias) like '%SUBSPACE:%' then 'SUBSPACE'

        when upper(raw.tags.ifAlias) like '%RIOTGAMES:%' then 'RIOTGAMES'

        when upper(raw.tags.ifAlias) like '%FASTLY:%' then 'FASTLY'

        when upper(raw.tags.ifAlias) like '%DE-CIX:%' then 'DECIX'

        when upper(raw.tags.ifAlias) like '%DEUTSCHE%TELEKOM%' then 'Deutsche Telekom'

        when upper(raw.tags.ifAlias) like '%TENCENT%' then 'Tencent'

        else 'Unknown'

      end

    when upper(raw.tags.ifAlias) like '%S:CDN%' then

       case

        when upper(raw.tags.ifAlias) like '%FACEBOOK%' then 'Facebook'

        when upper(raw.tags.ifAlias) like '%GOOGLE%' then 'Google'

        when upper(raw.tags.ifAlias) like '%NETFLIX%' then 'Netflix'

        when upper(raw.tags.ifAlias) like '%AKAMAI%' then 'Akamai'

        when upper(raw.tags.ifAlias) like '%VERIZON%' then 'Verizon'

        when upper(raw.tags.ifAlias) like '%LIMELIGHT%' then 'Limelight'

        when upper(raw.tags.ifAlias) like '%QWILT%' then 'QWILT'

        when upper(raw.tags.ifAlias) like '%APPLE%' then 'APPLE'

        else 'Unknown'

      end

    when upper(raw.tags.ifAlias) like '%S:TRANSITO%' then

       case

        when upper(raw.tags.ifAlias) like '%TATA%' then 'Tata'

        when upper(raw.tags.ifAlias) like '%COGENT%' then 'Cogent'

      end  

    when upper(raw.tags.ifAlias) like '%S:IPX-GRX%' then

       case

        when upper(raw.tags.ifAlias) like '%IBASIS%' then 'IBASIS'

        when upper(raw.tags.ifAlias) like '%ORANGE%' then 'ORANGE'

       end

    else null

  end servico

from `networkanalytics-p-292818.netmetrix.ports` raw

where raw.day_part = CURRENT_DATE("GMT")

and

(

    (

    (upper(raw.tags.ifAlias) like '% S:PEERING%' or upper(raw.tags.ifAlias) like '% S:CDN%' or upper(raw.tags.ifAlias) like '% S:TRANSITO%')

    and (upper(raw.tags.ifName) not like 'BUNDLE%' and upper(raw.tags.ifName) not like 'CE%' and upper(raw.tags.ifName) not like 'BVI%')

    )

or

    upper(raw.tags.ifAlias) like '% S:IPX-GRX%'

)

)
```
Prefiffy SQL:
```sql
SELECT raw.device.sysname node_fullname,  
       **Upper**(CASE  
               WHEN **Instr**(raw.device.sysname, '.', 1) <> 0 THEN  
               **Substr**(raw.device.sysname, 0, **Instr**(raw.device.sysname, '.', 1)  
                                             - 1)  
               ELSE raw.device.sysname  
             end)         node,  
       raw.tags.port_id   interfaceid,  
       raw.tags.ifname    interface,  
       raw.tags.ifalias   alias,  
       CASE  
         WHEN **Upper**(raw.tags.ifalias) LIKE '%S:PEERING%' THEN 'Peering'  
         WHEN **Upper**(raw.tags.ifalias) LIKE '%S:CDN%' THEN 'CDN'  
         WHEN **Upper**(raw.tags.ifalias) LIKE '%S:TRANSITO%' THEN 'Trânsito'  
         WHEN **Upper**(raw.tags.ifalias) LIKE '%S:IPX-GRX%' THEN 'IPX-GRX'  
         ELSE NULL  
       end                tipo,  
       CASE  
         WHEN **Upper**(raw.device.sysname) LIKE '%TLV1%' THEN 'Telvent'  
         WHEN **Upper**(raw.device.sysname) LIKE '%IMO1%' THEN 'Imopolis'  
         WHEN **Upper**(raw.device.sysname) LIKE '%PDG1%' THEN 'Ponta Delgada'  
         WHEN **Upper**(raw.device.sysname) LIKE '%CMP1%' THEN 'Campanhã'  
         WHEN **Upper**(raw.device.sysname) LIKE '%PCE1%' THEN 'Porto Centro'  
         WHEN **Upper**(raw.device.sysname) LIKE '%ESMAD1%'  
               OR **Upper**(raw.device.sysname) LIKE '%NLSPL1%' THEN 'Interxion'  
         WHEN **Upper**(raw.device.sysname) LIKE '%GBLON1%' THEN 'Telehouse'  
         WHEN **Upper**(raw.device.sysname) LIKE '%FUN1%' THEN 'Funchal'  
         WHEN **Upper**(raw.device.sysname) LIKE '%ESMAD2%' THEN 'Equinix'  
         ELSE NULL  
       end                localizacao,  
       CASE  
         WHEN **Upper**(raw.device.sysname) LIKE '%TLV1%' THEN 'Lisboa'  
         WHEN **Upper**(raw.device.sysname) LIKE '%IMO1%' THEN 'Lisboa'  
         WHEN **Upper**(raw.device.sysname) LIKE '%PDG1%' THEN 'Ponta Delgada'  
         WHEN **Upper**(raw.device.sysname) LIKE '%CMP1%' THEN 'Porto'  
         WHEN **Upper**(raw.device.sysname) LIKE '%PCE1%' THEN 'Porto'  
         WHEN **Upper**(raw.device.sysname) LIKE '%ESMAD%' THEN 'Madrid'  
         WHEN **Upper**(raw.device.sysname) LIKE '%GBLON1%' THEN 'Londres'  
         WHEN **Upper**(raw.device.sysname) LIKE '%FUN1%' THEN 'Funchal'  
         WHEN **Upper**(raw.device.sysname) LIKE '%NLSPL1%' THEN 'Amesterdão'  
         ELSE NULL  
       end                cidade,  
       CASE  
         WHEN **Upper**(raw.tags.ifalias) LIKE '%S:PEERING%' THEN  
           CASE  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%GOOGLE%' THEN 'Google'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%GIGAPIX%' THEN 'Gigapix'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%MICROSOFT%' THEN 'Microsoft'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%MEO%' THEN 'Meo'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%FACEBOOK%' THEN 'Facebook'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%AMAZON%' THEN 'Amazon'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%TWITCH%' THEN 'Twitch'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%LIMELIGHT%' THEN 'Limelight'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%ESPANIX%' THEN 'Espanix'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%LINX%' THEN 'Linx'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%CDN77%' THEN 'CDN77'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%VALVE%' THEN 'Valve'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%RETN%' THEN 'RETN'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%VERIZON%' THEN 'Verizon'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%NETFLIX%' THEN 'Netflix'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%VODAFONE%' THEN 'Vodafone'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%AKAMAI%' THEN 'Akamai'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%CLOUDFLARE%' THEN 'Cloudflare'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%APPLE%' THEN 'APPLE'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%BLIZZARD%' THEN 'BLIZZARD'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%3D-UBISOFT%' THEN 'i3D-UBISOFT'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%VOLTERRA%' THEN 'VOLTERRA'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%SUBSPACE:%' THEN 'SUBSPACE'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%SUBSPACE:%' THEN 'SUBSPACE'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%RIOTGAMES:%' THEN 'RIOTGAMES'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%FASTLY:%' THEN 'FASTLY'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%DE-CIX:%' THEN 'DECIX'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%DEUTSCHE%TELEKOM%' THEN  
             'Deutsche Telekom'  
             ELSE 'Unknown'  
           end  
         WHEN **Upper**(raw.tags.ifalias) LIKE '%S:CDN%' THEN  
           CASE  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%FACEBOOK%' THEN 'Facebook'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%GOOGLE%' THEN 'Google'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%NETFLIX%' THEN 'Netflix'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%AKAMAI%' THEN 'Akamai'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%VERIZON%' THEN 'Verizon'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%LIMELIGHT%' THEN 'Limelight'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%QWILT%' THEN 'QWILT'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%APPLE%' THEN 'APPLE'  
             ELSE 'Unknown'  
           end  
         WHEN **Upper**(raw.tags.ifalias) LIKE '%S:TRANSITO%' THEN  
           CASE  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%TATA%' THEN 'Tata'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%COGENT%' THEN 'Cogent'  
           end  
         WHEN **Upper**(raw.tags.ifalias) LIKE '%S:IPX-GRX%' THEN  
           CASE  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%IBASIS%' THEN 'IBASIS'  
             WHEN **Upper**(raw.tags.ifalias) LIKE '%ORANGE%' THEN 'ORANGE'  
           end  
         ELSE NULL  
       end                servico  
FROM   `networkanalytics-p-292818.netmetrix.ports` raw  
WHERE  raw.day_part = "2024-09-27"  
       AND ( ( ( **Upper**(raw.tags.ifalias) LIKE '% S:PEERING%'  
                  OR **Upper**(raw.tags.ifalias) LIKE '% S:CDN%'  
                  OR **Upper**(raw.tags.ifalias) LIKE '% S:TRANSITO%' )  
               AND **Upper**(raw.tags.ifname) NOT LIKE '%BUNDLE%' )  
              OR **Upper**(raw.tags.ifalias) LIKE '% S:IPX-GRX%' );
```

Schema:
- node_fullname >> STRING
- node >> STRING
- interfaceid >> INTEGER
- alias >> STRING
- tipo >> STRING
- localizacao >> STRING
- cidade >> STRING
- servico >> STRING

Questions:
- tags.port_id is unique in the SOURCE (aka libreNMS)?
- interface_peer_cdn_trans_id its generated automatically? Is it used for any usecase?

### Topics to tackle

1. Most efficient way to compute a metadata table inside Big query. Alternatives:
	1. Have a metadata table valid for only a day. 
		- (Pro) smaller metadata table
		- (Pro) easiest to maintain 
		- (Con) requires a huge ODS_5m table
	2. Have a metadata table like the 1st option but with append only
		- (Pro) lower column number in ods_5m
		- (Con) larger metadata table
	1. Have a metadata table with an insert if not exists
		- (Pro) best in class solution
		- (Con) extremely difficult to implement in a data warehouse where there is no primary key and duplicates erasure
2. How to dynamically generate a group by aggregation in Power BI
	- Currently only by selecting all filters the data is correct
	- Work in 
3. ODS_5m timestamp particularity
	- Currently we don't have metrics for each 5 min, we have millisecond timestamped data
	- Should we build some of numeric counter for hour_part? E.g. 0000 == 00:00, 2110 == 21:10
