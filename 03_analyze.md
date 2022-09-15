# 03_analyze

**\<TO\-DO\>:** Explain how to get to the DW \-\> hue \-\> Run interactive Impala queries & how to save queries to hue, so next time \- user can simply run from the saved queries list

### Passenger Manifest & Layover Reporting

**Query 1:**  Query to find all international flights: flights where destination airport country is not the same as origin airport country

```
SELECT DISTINCT
   flightnum,
   uniquecarrier,
   origin,
   dest,
   `month`,
   dayofmonth,
   `dayofweek`
FROM
   `airlines`.flights f,
   `airlines`.airports oa,
   `airlines`.airports da
WHERE
   f.origin = oa.iata
   and f.dest = da.iata
   And oa.country <> da.country
ORDER BY
   `month` ASC,
   dayofmonth ASC;
```

**Query 2:** Query to explore passenger manifest data:  do we have international connecting flights?

```
SELECT 
*
FROM 
   `airlines`.unique_tickets a, 
   `airlines`.flights o, 
   `airlines`.flights d,
   `airlines`.airports oa, 
   `airlines`.airports da  
WHERE
   a.leg1flightnum = o.flightnum
   AND a.leg1uniquecarrier = o.uniquecarrier 
   AND a.leg1origin = o.origin 
   AND a.leg1dest = o.dest 
   AND a.leg1month = o.month 
   AND a.leg1dayofmonth = o.dayofmonth
   AND a.leg1dayofweek = o.`dayofweek` 
   AND a.leg2flightnum = d.flightnum
   AND a.leg2uniquecarrier = d.uniquecarrier 
   AND a.leg2origin = d.origin 
   AND a.leg2dest = d.dest 
   AND a.leg2month = d.month 
   AND a.leg2dayofmonth = d.dayofmonth
   AND a.leg2dayofweek = d.`dayofweek` 
   AND d.origin = oa.iata 
   AND d.dest = da.iata 
   AND oa.country <> da.country ;
```

**Query 3:** Number of passengers on the airline that has long, planned layovers for an international flight

```
SELECT 
   a.leg1uniquecarrier as carrier, 
   count(a.leg1uniquecarrier) as passengers
FROM 
   `airlines`.unique_tickets a
where 
   a.leg2deptime - a.leg1arrtime>90
group by 
   a.leg1uniquecarrier;
```

**Query 4:** Number of passengers on airlines that have elongated layovers for an international flight caused by delayed connection

```
SELECT 
   a.leg1uniquecarrier as carrier, 
   count(a.leg1uniquecarrier) as passengers 
FROM 
   `airlines`.unique_tickets a, 
   `airlines`.flights o, 
   `airlines`.flights d
where 
       a.leg1flightnum = o.flightnum
   AND a.leg1uniquecarrier = o.uniquecarrier 
   AND a.leg1origin = o.origin 
   AND a.leg1dest = o.dest 
   AND a.leg1month = o.month 
   AND a.leg1dayofmonth = o.dayofmonth
   AND a.leg1dayofweek = o.`dayofweek` 
   AND a.leg2flightnum = d.flightnum
   AND a.leg2uniquecarrier = d.uniquecarrier 
   AND a.leg2origin = d.origin 
   AND a.leg2dest = d.dest 
   AND a.leg2month = d.month 
   AND a.leg2dayofmonth = d.dayofmonth
   AND a.leg2dayofweek = d.`dayofweek` 
   AND o.depdelay > 60
group by 
   a.leg1uniquecarrier;
```

**Query 5:** Number of passengers on airlines that have elongated layovers for an international flight caused by missed connection

```
SELECT 
   a.leg1uniquecarrier as carrier, 
   count(a.leg1uniquecarrier) as passengers
--   o.arrdelay as delay
FROM 
   `airlines`.unique_tickets a, 
   `airlines`.flights o, 
   `airlines`.flights d
where 
       a.leg1flightnum = o.flightnum
   AND a.leg1uniquecarrier = o.uniquecarrier 
   AND a.leg1origin = o.origin 
   AND a.leg1dest = o.dest 
   AND a.leg1month = o.month 
   AND a.leg1dayofmonth = o.dayofmonth
   AND a.leg1dayofweek = o.`dayofweek` 
   AND a.leg2flightnum = d.flightnum
   AND a.leg2uniquecarrier = d.uniquecarrier 
   AND a.leg2origin = d.origin 
   AND a.leg2dest = d.dest 
   AND a.leg2month = d.month 
   AND a.leg2dayofmonth = d.dayofmonth
   AND a.leg2dayofweek = d.`dayofweek` 
   AND d.deptime-o.arrtime < o.arrdelay-45
group by 
   a.leg1uniquecarrier;
```
