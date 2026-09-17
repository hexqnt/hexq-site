+++
title = "SQL запросы к БД RiskSpectrum"
date = 2021-03-07
description = "Запросы для замены Diamond-событий и выгрузки графа деревьев отказов."
slug = "riskspectrum-sql"
aliases = ["/2021/03/07/riskspectrum-sql.html"]
+++

Заменить тагнутые Diamond-события на сходные по индентификатору трансферные деревья:

```sql
Update FTNodes
set FTNodes.RecNum = DD.FT_RecNum, FTNodes.RecType = DD.FT_RecType, FTNodes.Transfer=-1
From
(
  select
  Events.ID,
  BE_FTNodes.RecNum as BE_RecNum,
  BE_FTNodes.RecType as BE_RecType,
  FT_FTNodes.RecNum as FT_RecNum,
  FT_FTNodes.RecType as FT_RecType
  from Events
  JOIN FT ON Events.ID=FT.ID
  JOIN FTNodes as BE_FTNodes ON Events.Num=BE_FTNodes.RecNum and Events.Type=BE_FTNodes.RecType
  JOIN FTNodes as FT_FTNodes ON FT.Num=FT_FTNodes.FTNum
  where Events.Type=5 AND Events.Symbol=2 AND Events.Tag=-1 AND FT_FTNodes.FatherGateNum=0
) as DD
where FTNodes.RecNum = DD.BE_RecNum and FTNodes.RecType = DD.BE_RecType
```

[Исходный Gist: `Diamond.sql`](https://gist.github.com/hexqnt/f17d4021349e510bfb4f09aa1792027b)

Выгрузить рёберный граф ДО:

```sql
/*
Получить граф функциональные события (Function Event) - деревья отказов (Fault Tree)
*/
SELECT DISTINCT
FE.ID AS Node1,
FT.ID AS Node2,
'FE-FT' AS TYPE,
case Gates.Symbol
  when 100 then 'or'
  when 200 then 'and'
  when 300 then 'k/n'
  when 400 then 'nor'
end as Gate
FROM EVENTS AS FE
JOIN FEInputs ON FEInputs.FENum=FE.Num AND FEInputs.FEType=FE."Type"
JOIN EVENTS AS Gates ON Gates.Num = FEInputs.InputNum AND Gates.type = FEInputs.InputType
JOIN FTNodes ON FTNodes.RecNum = Gates.Num AND FTNodes.RecType = Gates.Type
JOIN FT ON FT.Num = FTNodes.FTNum
WHERE FE.Type = 11 AND Gates."Type"=6 AND FTNodes.FatherGateNum=0
UNION
/*
Получить граф дерево отказов (Fault Tree) - дерево отказов (Fault Tree)
*/
SELECT DISTINCT
FT.ID AS Node1,
rFT.ID AS Node2,
'FT-FT' AS TYPE,
case Gates.Symbol
  when 100 then 'or'
  when 200 then 'and'
  when 300 then 'k/n'
  when 400 then 'nor'
end as Gate
FROM FT
JOIN FTNodes ON FTNodes.FTNum = FT.Num
JOIN EVENTS AS Gates ON Gates.Num = FTNodes.RecNum AND Gates.Type = FTNodes.RecType
JOIN FTNodes AS rFTNodes ON rFTNodes.RecNum = Gates.Num AND rFTNodes.RecType = Gates.Type
JOIN FT AS rFT ON rFT.Num = rFTNodes.FTNum
WHERE
rFTNodes.FatherGateNum = 0 AND rFTNodes.RecType != 21 AND rFTNodes.RecType = 6 AND
FTNodes.FatherGateNum != 0 AND FTNodes.Transfer = -1
ORDER BY TYPE, Node1, Node2
```

[Исходный Gist: `GetFTGraph.sql`](https://gist.github.com/hexqnt/9fab3c2105a33e57ce0747e13edcbb52)
