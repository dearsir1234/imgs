<a name="O0PY8"></a>
# 一、sql的最基本用法
<a name="A10jh"></a>
## 1.直接查询
```sql
 select dur,ts,name from slice
```
我们可以看到<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722057985230-4fa06251-f776-4f0d-896d-02aa3947caf6.png#averageHue=%23eff0ef&clientId=u2641df99-0a5c-4&from=paste&height=565&id=u827228a1&originHeight=847&originWidth=2153&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=148779&status=done&style=none&taskId=uc80f138e-e925-472c-8197-b2712b2a8e4&title=&width=1435.3333333333333)<br />我们来看看这个语句是什么意思：
:::info
ts: 展示timestamp 时间戳<br />dur: 表示需要询问的事件的时长<br />name: 表示查询的对象名<br />slice: 表示 trace 上一段时间的事件集合<br /> select dur,ts,name from slice：从trace时间集合中获取时间戳、事件时长、对象名
:::
从公式上我们再看看下面的查询
:::tips

- $\pi_{\text{dur, ts, name}}(\text{slice})$
:::

<a name="eD2R6"></a>
## 2.带限制条件的查询
我们首先看一下具体的查询语句
```sql
SELECT ts, dur, name FROM slice WHERE ts > 	261187021827611 AND ts < 261187022237871;
```
运行完结果应该是这样的<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722059448429-c6252634-dc9b-4015-a705-7d360cee1e35.png#averageHue=%23a6aaa0&clientId=u0adfd262-f993-4&from=paste&height=541&id=u5cdff928&originHeight=811&originWidth=2165&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=135061&status=done&style=none&taskId=uc3cb370a-7db0-4d29-9045-1c433009d1d&title=&width=1443.3333333333333)<br />我们从公式上看是这样的
:::tips
$\pi_{\text{dur, ts, name}} \left( \sigma_{261187021827611 < \text{ts} < 261187022237871} (\text{slice}) \right)$
:::
接下来我们从关系代数的角度上来看一下查询步骤
<a name="BMlNQ"></a>
### 1.选择操作
:::tips
$\sigma_{261187021827611 < \text{ts} < 261187022237871} (\text{slice})$
:::
使用选择运算符$σ$来过滤出满足 `ts > 261187021827611 AND ts < 261187022237871` 的记录
<a name="f6soS"></a>
### 2.投影操作
:::tips
$\pi_{\text{dur, ts, name}} \left( \sigma_{261187021827611 < \text{ts} < 261187022237871} (\text{slice}) \right)$
:::
 使用投影运算符$π$来选择 `dur`、`ts` 和 `name` 列  
<a name="FZanr"></a>
## 3.带数量条件限制的查询
我们还是从最基本的查询语句入手
```sql
SELECT * FROM slice LIMIT 10
```
我们可以看到我们很快就查到了结果：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722060668522-2cd3d7c9-e053-42e7-b107-bf28c6a754d7.png#averageHue=%23c2c7bd&clientId=ua50c9ca3-37b3-4&from=paste&height=459&id=u5de1783f&originHeight=689&originWidth=2171&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=181105&status=done&style=none&taskId=ude8bd9ee-49b4-4209-8759-3d6711a0cfa&title=&width=1447.3333333333333)<br />接着我们还是从关系代数上入手
:::tips
$\tau_{10} (\text{slice})$
:::
 这表示从表 `slice` 中选择前10条记录。  
<a name="KMl2A"></a>
## 4.对表属性的重新命名
我们还是先看查询语句
```sql
SELECT dur, ts AS timestart, name FROM slice LIMIT 10;
```
从查询结果中我们可以看出来，原本的ts列变成了timestart列<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722062185778-827bee21-4627-4652-9da1-3ca5a714bbd9.png#averageHue=%23979b96&clientId=ua50c9ca3-37b3-4&from=paste&height=458&id=u65773431&originHeight=687&originWidth=2139&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=105755&status=done&style=none&taskId=u2e5de820-c1e3-45dc-84b1-9582f6d7798&title=&width=1426)<br />我们接着从关系代数上看一下查询语句
:::tips
$\tau_{10} (\pi_{\text{dur, timestart, name}} (\rho_{\text{timestart} / \text{ts}} (\text{slice})))$
:::
$\rho_{\text{timestart} / \text{ts}} (\text{slice})$：

- $ρ$:重命名运算符。  
- timestart/ts：将 `ts` 列重命名为 `timestart`。  
- slice：原始表。  
<a name="GjY1o"></a>
## 5.使用 LIKE 进行模糊查询  
```sql
SELECT name FROM slice WHERE name LIKE '%wait%';
```
 从查询结果中我们可以看出来，所有以字母含有'wait'的name列的值都被选中了  <br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722089397641-f235d0ca-0263-4686-abb1-570e64c432ba.png#averageHue=%23ced0d2&clientId=u9637a24f-4e32-4&from=paste&height=677&id=ubfcbfbc2&originHeight=1015&originWidth=2129&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=234611&status=done&style=none&taskId=uaf1b14a0-134b-4fc3-99c0-eaf09c226ad&title=&width=1419.3333333333333)<br />我们还是从关系代数上看一下下面的sql语句
:::tips
$$\sigma_{\text{name} \text{ LIKE '%A%'}} (\text{slice})$$
:::

补充内容：选择以wait开头
```sql
SELECT name FROM slice WHERE name LIKE 'wait%';
```
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722089572561-019c772e-0cd3-4767-a517-48733004b96a.png#averageHue=%23eff0ef&clientId=u9637a24f-4e32-4&from=paste&height=524&id=u5da367bf&originHeight=786&originWidth=2149&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=71990&status=done&style=none&taskId=u916198aa-e08a-4266-b9d4-2fee5f96085&title=&width=1432.6666666666667)<br />补充内容：选择以wait结尾
```sql
SELECT name FROM slice WHERE name LIKE '%wait';
```
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722089652370-39bbb924-8083-4b7d-a76f-b21a4e350784.png#averageHue=%23eff0ef&clientId=u9637a24f-4e32-4&from=paste&height=449&id=u6aabee33&originHeight=674&originWidth=2151&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=64617&status=done&style=none&taskId=u09a63921-ff12-4685-9bd7-18a1f2b721d&title=&width=1434)
<a name="JODoQ"></a>
## 6.查询时忽略大小写
```sql
SELECT * FROM slice WHERE LOWER(name) LIKE "%wait%" LIMIT 50
```
我们还是先看看运行结果<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722141174239-0bc3ca30-38c5-42df-a934-358491494858.png#averageHue=%23dcddca&clientId=u76432bc5-74bb-4&from=paste&height=652&id=uf6eb6d48&originHeight=1076&originWidth=2179&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=318358&status=done&style=none&taskId=ud7e67d93-5e0b-41fb-bc06-b36ffe95a03&title=&width=1320.6059842770903)<br />这个可以用来在你不确定函数大小写的时候使用。这个不包含关系代数的算法内容，便不再给出关系代数内容。
<a name="QadQd"></a>
# 二、简单实战
<a name="YrUwA"></a>
## 1.分析特定事件的发生频率：
```sql
SELECT COUNT(*) FROM slice WHERE name = 'postAndWait'
```
我们先来看一下运行结果：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722085006653-0cd537ea-6011-400c-9f1c-4e1f1ef5f757.png#averageHue=%23777f79&clientId=u1826873d-abfb-4&from=paste&height=247&id=ub4c02435&originHeight=370&originWidth=2146&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=35289&status=done&style=none&taskId=udb39eea9-b235-46c7-b3bf-3196cf7b557&title=&width=1430.6666666666667)<br />不难看出postAndWait运行了93次

<a name="B8TP4"></a>
## 2.分析事件的性能数据：
```sql
SELECT (dur/1e6) FROM slice WHERE name = 'postAndWait'
```
我们再看看运行结果：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722085318073-b1fa627b-81eb-43e0-832a-bd29d533fdfb.png#averageHue=%235a5f57&clientId=u9637a24f-4e32-4&from=paste&height=601&id=uf5fd1546&originHeight=901&originWidth=2126&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=81659&status=done&style=none&taskId=u9fb846d6-7702-4317-9c33-006759a42fa&title=&width=1417.3333333333333)<br />我们可以看到对应时间的运行时间（单位纳秒）<br />备注：dur是运行时间的意思，le6指的是10的6次方，在科学计数法中 le6代表1 × 10^6  

<a name="RKolE"></a>
## 3.分析一段时间内的运行数据：
```sql
SELECT MIN(dur/1e6) as min_duration, MAX(dur/1e6) as max_duration, AVG(dur/1e6) as avg_duration FROM slice WHERE name = 'postAndWait' and dur > 0
```
我们再来看看运行结果：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722087822384-13ed1a8e-abdd-4ded-b3fc-78ab7b5fdab2.png#averageHue=%23949d96&clientId=u9637a24f-4e32-4&from=paste&height=247&id=ub0b93784&originHeight=370&originWidth=2145&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=61323&status=done&style=none&taskId=ub807c796-b3a0-46a9-b3e3-80ec058a693&title=&width=1430)<br />我们从图上可以看出最短运行时间为0.2294纳秒，最长运行时间为74.36纳秒，平均运行时间为4.16纳秒<br />下面我们从关系代数上看看下面<br />$\begin{align*}
&\text{定义一个关系：} \\
&\sigma_{(\text{name} = 'postAndWait') \land (\text{dur} > 0)}(\text{slice}) \\
&\text{然后进行聚合操作：} \\
&\gamma_{\text{MIN}(\frac{\text{dur}}{10^6}) \to \text{min\_duration}, \text{MAX}(\frac{\text{dur}}{10^6}) \to \text{max\_duration}, \text{AVG}(\frac{\text{dur}}{10^6}) \to \text{avg\_duration}} \\
&\quad \left(\sigma_{(\text{name} = 'postAndWait') \land (\text{dur} > 0)}(\text{slice})\right)
\end{align*}$<br />我们可以分三步看上面的操作：

- 选择操作：从 `slice` 表中选择 `name = 'postAndWait'` 且 `dur > 0` 的记录。
- 投影操作：选择 `dur` 列。
- 聚合操作：计算最小值、最大值和平均值。
<a name="gftkz"></a>
## 4.对事件进行排序
```sql
SELECT (dur/1e6),ts,name FROM slice WHERE name LIKE '%running%' and dur > 0 ORDER by dur DESC
```
我们接着看看下面的运行结果：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722089134422-4717179d-a3c5-4d0a-8e0e-5105d8f9cc43.png#averageHue=%23d0d3ce&clientId=u9637a24f-4e32-4&from=paste&height=682&id=u3fbd2210&originHeight=1023&originWidth=2190&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=304635&status=done&style=none&taskId=uacdd0e8a-6a1a-4bec-8fa5-bdd9e99c730&title=&width=1460)<br />可以分为以下几个步骤：<br />**1.选择运算（σ）：选择满足条件 **`**name LIKE '%running%'**`** 和 **`**dur > 0**`** 的元组**<br />$\sigma_{\text{name LIKE '%running%' AND dur > 0}} (\text{slice})$<br />**2.投影运算（π）：投影列 **`**(dur/1e6), ts, name**`<br />$\pi_{(\text{dur/1e6}), ts, name} (\sigma_{\text{name LIKE '%running%' AND dur > 0}} (\text{slice}))$<br />**3.排序（τ）：按 **`**dur DESC**`** 进行降序排序**<br />$\tau_{\text{dur DESC}} (\pi_{(\text{dur/1e6}), ts, name} (\sigma_{\text{name LIKE '%running%' AND dur > 0}} (\text{slice})))$
<a name="exTpQ"></a>
## 5.查询CPU支持的频率
```sql
 select * from cpu_freq
```
我们还是先看看运行结果：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722141007252-59f6bd88-c866-4f7b-8ce1-2b58b6910550.png#averageHue=%23767f79&clientId=u76432bc5-74bb-4&from=paste&height=368&id=u69369b1b&originHeight=608&originWidth=2147&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=72273&status=done&style=none&taskId=u41410418-9466-444e-bc2d-7a2f65ba018&title=&width=1301.2120460040903)

<a name="hZ6XX"></a>
# 三、切片定向时间段获取
<a name="pNUSl"></a>
## 1.我们在查询时最好先优化一下视图
```sql
DROP VIEW IF EXISTS slice_with_utid;
CREATE VIEW slice_with_utid AS
SELECT
  ts,
  dur,
  slice.name as slice_name,
  slice.id as slice_id, utid,
  thread.name as thread_name
FROM slice
JOIN thread_track ON thread_track.id = slice.track_id
JOIN thread USING (utid);

DROP TABLE IF EXISTS slice_thread_state_breakdown;
CREATE VIRTUAL TABLE slice_thread_state_breakdown
USING SPAN_LEFT_JOIN(
  slice_with_utid PARTITIONED utid,
  thread_state PARTITIONED utid
);
```

- 首先创建一个视图 `slice_with_utid`，该视图将 `slice` 表和 `thread` 表的数据进行连接并选择特定的列。
- 然后，它创建了一个虚拟表 `slice_thread_state_breakdown`，该表通过 `SPAN_LEFT_JOIN` 方法将 `slice_with_utid` 视图和 `thread_state` 表的数据进行左连接，从而生成一个包含两个分区数据的连接结果集。
<a name="jwby9"></a>
## 2.跟踪中所有切片的 CPU 时间
```sql
SELECT slice_id, slice_name, SUM(dur) AS cpu_time
FROM slice_thread_state_breakdown
WHERE state = 'Running'
GROUP BY slice_id;
```
我们还是先看看运行结果<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722091317951-0f1b6e24-2818-4b3e-bb12-7139f0283d3b.png#averageHue=%23d0d1d4&clientId=u9637a24f-4e32-4&from=paste&height=544&id=ue253cb6b&originHeight=898&originWidth=2143&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=163805&status=done&style=none&taskId=u2aeb6480-3cff-494d-b95e-530bf36e692&title=&width=1298.7878037199655)<br />我们依旧从关系代数的角度看看逻辑关系<br />具体关系代数操作如下：<br />**选择操作**：从 `slice_thread_state_breakdown` 表中选择 `state` 为 `Running` 的记录：<br />$\sigma_{\text{state} = \text{'Running'}}(\text{slice\_thread\_state\_breakdown})$<br />**投影操作**：从上一步的结果中投影出 `slice_id`, `slice_name` 和 `dur` 列：<br />$\pi_{\text{slice\_id}, \text{slice\_name}, \text{dur}}(\sigma_{\text{state} = \text{'Running'}}(\text{slice\_thread\_state\_breakdown}))$<br />**聚合操作**：按 `slice_id` 分组并计算 `dur` 列的总和：<br />$\text{SUM}(\text{dur}) \text{ GROUP BY } \text{slice_id}$
<a name="xTFDT"></a>
## 3.计算特定切片的 CPU 时间
```sql
SELECT slice_name, SUM(dur) AS cpu_time
FROM slice_thread_state_breakdown
WHERE slice_id = 18 AND state = 'Running';
```
我们还是先运行一下看看效果：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722092271410-4d6762d6-1759-4708-b87b-c2f6e74cdada.png#averageHue=%235f6660&clientId=u8a50ad7b-7611-4&from=paste&height=206&id=ua51a1847&originHeight=340&originWidth=2129&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=57341&status=done&style=none&taskId=u86726e80-01c6-4653-a8be-7bdc179151f&title=&width=1290.302955725528)<br />我们不难看出binder transaction花费的时间片为155400<br />紧接着我们还是用关系代数解释一下sql语句的运行步骤：<br />**1.选择（σ）**: 选择操作用于从关系中筛选出满足特定条件的元组。对于这个查询，我们要从表 `slice_thread_state_breakdown` 中选择 `slice_id` 为 18 且 `state` 为 'Running' 的记录  <br />$\sigma_{slice\_id = 18 \wedge state = 'Running'}(\text{slice\_thread\_state\_breakdown})$<br />**2.投影（π）**: 投影操作用于从关系中选择指定的属性列。在这个查询中，我们需要选择 `slice_name` 和 `dur` 这两个属性。  <br />$\pi_{slice\_name, dur}(\sigma_{slice\_id = 18 \wedge state = 'Running'}(\text{slice\_thread\_state\_breakdown}))$<br />**3.聚合（γ）**: 聚合操作用于计算分组数据的统计信息。在这个查询中，我们对 `slice_name` 进行分组，并对每个分组计算 `dur` 的总和，结果命名为 `cpu_time`。  <br />$\gamma_{slice\_name; \text{SUM}(dur) \rightarrow cpu\_time}(\pi_{slice\_name, dur}(\sigma_{slice\_id = 18 \wedge state = 'Running'}(\text{slice\_thread\_state\_breakdown})))$
<a name="BiboE"></a>
## 4.查询变体:runnable_time和uninterruptible_time
我们可以依据上面的sql语句进行简单变形，例如，要获取“runnable_time”和“uninterruptible_time”所花费的时间
```sql
SELECT
  slice_id,
  slice_name,
  SUM(IIF(state = 'R', dur, 0)) AS runnable_time,
  SUM(IIF(state = 'D', dur, 0)) AS uninterruptible_time
FROM slice_thread_state_breakdown
GROUP BY slice_id;
```
老样子，我们还是运行一下看看效果<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722092916658-70e5a4c1-2f12-4c68-b43a-ff6fcfe06733.png#averageHue=%23778379&clientId=u6463a5a0-61d5-4&from=paste&height=451&id=u6794c26e&originHeight=744&originWidth=2120&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=139707&status=done&style=none&taskId=u93dc6559-3f2b-4f3e-b6c9-a48468e6a89&title=&width=1284.8484105862467)<br />关系代数与之前查询类似，便不再提供
<a name="MOPw6"></a>
# 四、通过唤醒线程计算调度时间
<a name="ueEBX"></a>
## 1.对单个线程进行计算
```sql
SELECT
  SUM((
    SELECT dur FROM sched
    WHERE
      sched.ts > wakee_runnable.ts AND
      wakee_runnable.utid = wakee_runnable.utid
    ORDER BY ts
    LIMIT 1
  )) AS scheduled_dur
FROM thread AS waker
JOIN thread_state AS wakee_runnable ON waker.utid = wakee_runnable.waker_utid
WHERE waker.name = <your waker thread name here>
```
下面我运行一下试试，我唤醒的线程为'm.example.aop_1'，它是我写的一个程序：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722093912401-e5f879eb-c70b-4494-ac32-108ecba0474c.png#averageHue=%236d756e&clientId=u6463a5a0-61d5-4&from=paste&height=218&id=u93f071d1&originHeight=359&originWidth=2124&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=66946&status=done&style=none&taskId=u072be41e-6c21-418d-b562-dbaa8d96bba&title=&width=1287.2726528703718)<br /> 我们可以这样了解为特定的 `waker` 线程计算它所关联的 `wakee_runnable` 状态下的计划持续时间。具体地，它从 `sched` 表中查找所有计划时间在 `wakee_runnable` 状态时间之后的记录，然后对这些记录的持续时间进行求和。<br />它比较复杂，我们分内外两部来看：
<a name="fbarh"></a>
### 内部子查询的关系代数  
```sql
SELECT dur FROM sched
WHERE sched.ts > wakee_runnable.ts AND wakee_runnable.utid = wakee_runnable.utid
ORDER BY ts
LIMIT 1
```

- **选择**（Selection）：`σ_sched.ts > wakee_runnable.ts ∧ wakee_runnable.utid = wakee_runnable.utid (sched)`
- **排序**（Sorting）：按 `ts` 升序排列
- **限制**（Limiting）：选择前 1 条记录
<a name="FvcIz"></a>
###  外部查询的关系代数  
```sql
SELECT SUM((
    SELECT dur FROM sched
    WHERE
      sched.ts > wakee_runnable.ts AND
      wakee_runnable.utid = wakee_runnable.utid
    ORDER BY ts
    LIMIT 1
)) AS scheduled_dur
FROM thread AS waker
JOIN thread_state AS wakee_runnable ON waker.utid = wakee_runnable.waker_utid
WHERE waker.name = <your waker thread name here>
```
<a name="e49e322e"></a>
#### 2.1. 连接操作

- **连接**（Join）：`thread ⨝ (thread.utid = thread_state.waker_utid) thread_state`
<a name="b889742f"></a>
#### 2.2. 选择操作

- **选择**（Selection）：`σ_waker.name = <your waker thread name here> (thread ⨝ (thread.utid = thread_state.waker_utid) thread_state)`
<a name="3b33cbc0"></a>
#### 2.3. 聚合操作

- **聚合**（Aggregation）：对满足条件的记录应用 `SUM` 聚合函数来计算 `scheduled_dur`
<a name="rCDHB"></a>
## 2.对所有线程进行该操作
```sql
SELECT
  waker_process.name AS process_name,
  waker.name AS thread_name,
  SUM((
    SELECT dur FROM sched
    WHERE
      sched.ts > wakee_runnable.ts AND
      sched.utid = wakee_runnable.utid
    ORDER BY ts
    LIMIT 1
  )) AS scheduled_dur
FROM thread AS waker
JOIN process AS waker_process USING (upid)
JOIN thread_state AS wakee_runnable ON waker.utid = wakee_runnable.waker_utid
WHERE waker.utid != 0
GROUP BY 1, 2
ORDER BY 3 desc
```
我们运行一下看看：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722094521161-151ef901-f95e-4c95-a06e-3c4e4b28fddc.png#averageHue=%23778075&clientId=u6463a5a0-61d5-4&from=paste&height=710&id=u1a21b598&originHeight=1171&originWidth=2121&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=268752&status=done&style=none&taskId=ucdee14f1-7f0a-4544-b36f-ea5cb4a2e02&title=&width=1285.454471157278)<br />代数操作与上一查询相同，不再使用关系代数解释
<a name="hmk1D"></a>
# 五、对帧进行分析
<a name="X3dyJ"></a>
## 1.首先查看一下要分析的帧
```sql
SELECT * FROM slice WHERE name LIKE '%Choreographer#doFrame%';
```
我们运行一遍看看效果<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722142508353-5c786c92-1d11-4047-abb8-49fe2e568c58.png#averageHue=%23afb59e&clientId=u76432bc5-74bb-4&from=paste&height=600&id=uc59218ab&originHeight=990&originWidth=2132&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=248522&status=done&style=none&taskId=u50bd3987-c758-4a00-975f-ee65b27ed58&title=&width=1292.1211374386216)
<a name="nLpgF"></a>
## 2.统计处理帧率
```sql
SELECT COUNT(*)/((MAX(ts) - MIN(ts))/1e9) AS Request_FPS FROM slice WHERE name LIKE '%Choreographer#doFrame%';
```
我们还是看一下运行结果<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722142607562-a133c4bf-d95f-4aa9-8dd8-aa4c797540c2.png#averageHue=%23656d65&clientId=u76432bc5-74bb-4&from=paste&height=205&id=u9152cb1c&originHeight=338&originWidth=2137&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=57146&status=done&style=none&taskId=ue92dde7e-3407-4f64-9ab4-6d7a72d8dd4&title=&width=1295.151440293778)<br />很明显从帧数上来看每秒44帧左右<br />查询条件可以从以下4步来理解：

- **过滤条件**：从表 `slice` 中选择 `name` 列中包含 `Choreographer#doFrame` 的所有记录。
- **计算最大和最小时间戳的差值**：从筛选后的记录中找到最大 (`MAX(ts)`) 和最小 (`MIN(ts)`) 的时间戳，并计算它们之间的差值。
- **计算记录数**：计算筛选后的记录数 (`COUNT(*)`)。
- **计算每秒请求数**：将记录数除以时间戳差值（单位换算为秒）来计算每秒请求数。

关系代数表达：<br />1.选择操作： 将表 `slice` 中满足 `name` 列包含 `Choreographer#doFrame` 的记录选出来。    <br />$\sigma_{\text{name LIKE '%Choreographer#doFrame%'}}(\text{slice})$<br />2.聚合操作：

- 计算最大时间戳：$MAX(ts)$
- 计算最小时间戳：$MIN(ts)$
- 计算记录数：$COUNT(∗)$

3.计算每秒请求数：将记录数除以时间戳差值并转换为秒。  
<a name="irxwZ"></a>
## 3.统计某线程的帧率
```sql
SELECT COUNT(*)/((MAX(ts) - MIN(ts))/1e9) AS FPS
FROM slice
JOIN thread_track ON slice.track_id = thread_track.id
JOIN thread USING(utid)
WHERE slice.name LIKE '%Choreographer#doFrame%' AND thread.name like '%m.example.aop_1%'
```
我们运行一下看看结果<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722144542223-f23cfd62-e24a-4311-876e-b67ee7347d91.png#averageHue=%23626a63&clientId=u290860d4-ae89-4&from=paste&height=199&id=ucc789396&originHeight=328&originWidth=2150&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=63309&status=done&style=none&taskId=u33a97e92-635d-4e4c-9480-abd728c562c&title=&width=1303.030227717184)<br />很明显我们可以看出帧数为13.7<br />我们还是先分解一下sql语句的执行步骤：

- **连接操作**：
- 连接 `slice` 和 `thread_track` 表，通过 `track_id` 和 `id`。
- 连接 `thread_track` 和 `thread` 表，通过 `utid`。
- **过滤条件**：
- `slice.name` 中包含 `Choreographer#doFrame`。
- `thread.name` 中包含 `m.example.aop_1`。
- **计算时间跨度**：
- 计算时间戳的最大值 (`MAX(ts)`) 和最小值 (`MIN(ts)`)。
- **计算记录数**：
- 计算满足条件的记录数 (`COUNT(*)`)。
- **计算每秒请求数**：
- 将记录数除以时间戳差值（单位换算为秒）来计算每秒请求数。

紧接着我们从关系代数的角度看一下执行步骤：<br />1. 选择操作：<br />$R_1 = \sigma_{\text{slice.name LIKE '%Choreographer#doFrame%'}}(\text{slice})\\
R_2 = \sigma_{\text{thread.name LIKE '%m.example.aop_1%'}}(\text{thread})$  <br />2.连接操作：<br />$R_3 = R_1 \bowtie_{\text{slice.track_id = thread_track.id}} \text{thread_track}\\
R_4 = R_3 \bowtie_{\text{thread_track.utid = thread.utid}} R_2$<br />3.聚合操作：<br />$\begin{align*}

\text{max\_ts} &= \text{MAX}(\pi_{\text{ts}}(R_4)) \\
\text{min\_ts} &= \text{MIN}(\pi_{\text{ts}}(R_4)) \\
\text{count} &= \text{COUNT}(\ast) \\

\end{align*}$<br />4.计算每秒请求数<br />$\begin{align*}
\text{FPS} &= \frac{\text{count}}{\left( \text{max\_ts} - \text{min\_ts} \right) / 1e9}
\end{align*}$
<a name="jdHmp"></a>
# 六、对内存进行分析
<a name="SUOpw"></a>
## 1.我们首先回忆一下内存基本概念
:::tips
        mem.locked     VmLck     VmLck代表进程已经锁住的物理内存的大小.锁住的物理内存不能交换到硬盘<br />        mem.rss     VmRSS     RSS值，这里的值是RssAnon、RssFile和RssShmem的和<br />        mem.rss.anon     RssAnon     匿名RSS内存大小<br />        mem.rss.file     RssFile     文件RSS内存大小<br />        mem.rss.shmem     RssShmem     共享内存RSS内存大小<br />        mem.rss.watermark     VmHWM     Peak RSS(“high water mark”).<br />        mem.swap     VmSwap     进程Swap内存大小<br />        mem.virt     VmSize     进程虚拟内存大小<br />        oom_score_adj     oom_score_adj     当前该进程的OOM Score ADJ值
:::
<a name="JjAPo"></a>
## 2.我们对具体进程的内存进行查询操作：
```sql
    SELECT c.ts, c.value, t.name as counter_name, p.name as proc_name, p.pid
        FROM counter as c
        LEFT JOIN process_counter_track as t ON c.track_id = t.id
        LEFT JOIN process as p USING (upid)
        WHERE t.name LIKE 'mem.%' AND proc_name LIKE '%com.example.aop_1%'
```
我们运行一下看看结果：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722147103105-17a20056-d6a9-4b6d-bf2c-73b5c89f359e.png#averageHue=%23e0e1cc&clientId=u90519467-4fa0-4&from=paste&height=684&id=ubfd4268e&originHeight=1026&originWidth=2155&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=302756&status=done&style=none&taskId=u2aeb8038-1387-4679-b1e6-c7325cc67d8&title=&width=1436.6666666666667)<br />很好，我们现在能清楚地看到com.example.aop_1进程的内存分布<br />首先我们先分解一下sql语句：

- **选择操作**：从 `process_counter_track` 表和 `process` 表中分别选择满足条件的记录。
- **连接操作**：将筛选后的 `counter` 表与 `process_counter_track` 表通过 `track_id` 连接，然后再与 `process` 表通过 `upid` 连接。
- **投影操作**：选择所需的列。

 关系代数表示 ：<br />**1.选择操作**：  <br />$\begin{align*}
R_1 &= \sigma_{\text{t.name LIKE 'mem.\%'}}(\text{process\_counter\_track}) \\
R_2 &= \sigma_{\text{p.name LIKE '\%com.example.aop\_1\%'}}(\text{process}) \\
\end{align*}$<br />**2.连接操作：**<br />$\begin{align*}
R_3 &= \text{counter} \ \text{LEFT JOIN} \ R_1 \ \text{ON} \ \text{counter.track\_id} = \text{R_1.id} \\
R_4 &= R_3 \ \text{LEFT JOIN} \ R_2 \ \text{USING} \ (\text{upid}) \\
\end{align*}$<br />**3.投影操作：**<br />$\begin{align*}
R_5 &= \pi_{\text{c.ts, c.value, t.name AS counter\_name, p.name AS proc\_name, p.pid}}(R_4)
\end{align*}$
<a name="BFchq"></a>
# 七、perfetto表
<a name="dOlHW"></a>
## 1.备注
对某个表需要详细了解的请阅读下面的文档：[PerfettoSQL Prelude - Perfetto Tracing Docs](https://perfetto.dev/docs/analysis/sql-tables#tables-diagram)
<a name="Dfo1Q"></a>
## 2.Tracks tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722149371493-194ecf8a-10d1-4862-a69f-2fa99b1b3f24.png#averageHue=%23fcfcfc&clientId=u90519467-4fa0-4&from=paste&height=422&id=u2aa65045&originHeight=633&originWidth=1763&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=69483&status=done&style=none&taskId=uc6e110c5-11ca-4a25-929d-b65bd977acb&title=&width=1175.3333333333333)
<a name="w76L4"></a>
## 3.Events tables<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156133516-b65a42d4-5cad-4d58-a2b5-a9a90136cbc4.png#averageHue=%23fbfbfb&clientId=ue6173c60-5ac8-4&from=paste&height=338&id=ube5882a4&originHeight=507&originWidth=1728&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=52466&status=done&style=none&taskId=uff160d8d-e921-4da3-b31b-928d99fc9ff&title=&width=1152)
<a name="PtMiO"></a>
## 4.Android tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156189831-8f51d917-fbfb-4837-99bf-340546fe354a.png#averageHue=%23fbfbfb&clientId=ue6173c60-5ac8-4&from=paste&height=322&id=u78292c31&originHeight=483&originWidth=1697&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=47452&status=done&style=none&taskId=ua5a8a804-eed3-4635-97ba-3ea9b798ba1&title=&width=1131.3333333333333)
<a name="NxiM1"></a>
## 5.ART Heap Graphs tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156222720-5cbcc419-6069-4902-b0b0-996cf3b218ec.png#averageHue=%23fafafa&clientId=ue6173c60-5ac8-4&from=paste&height=379&id=u0ce6bf11&originHeight=568&originWidth=740&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=34683&status=done&style=none&taskId=ua036dc1a-6f28-4ced-afbd-0360765b26f&title=&width=493.3333333333333)
<a name="LRVeX"></a>
## 6.Callstack profilers tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156258485-8fb9bba0-7384-4164-b9de-f820070348d6.png#averageHue=%23fafafa&clientId=ue6173c60-5ac8-4&from=paste&height=557&id=u75114f63&originHeight=835&originWidth=1695&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=89020&status=done&style=none&taskId=u535f7e24-3751-47c9-835e-a8d2244778d&title=&width=1130)
<a name="MWaOx"></a>
## 7.Chrome tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156292346-5d465a8a-6109-4227-b767-6a3a5464a2bc.png#averageHue=%23fafafa&clientId=ue6173c60-5ac8-4&from=paste&height=137&id=ue68b6298&originHeight=206&originWidth=690&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=10253&status=done&style=none&taskId=u05f4602d-cb57-4240-9d9f-c05088bffbc&title=&width=460)
<a name="elHeg"></a>
## 8.Counter Tracks tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156328183-e0322fed-5200-493d-9f95-e70f1f007899.png#averageHue=%23fcfcfc&clientId=ue6173c60-5ac8-4&from=paste&height=299&id=u98d1833f&originHeight=449&originWidth=1716&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=48340&status=done&style=none&taskId=udf193da8-56d3-4224-834f-3d8de5717ab&title=&width=1144)
<a name="bDdb8"></a>
## 9.jit tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156373510-72be78fb-48e5-4d1d-90d1-df499ba1eb14.png#averageHue=%23fbfbfb&clientId=ue6173c60-5ac8-4&from=paste&height=227&id=uc376e346&originHeight=340&originWidth=924&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=22717&status=done&style=none&taskId=u342cc670-50c9-4a14-a01a-20aec14f522&title=&width=616)
<a name="lizlA"></a>
## 10.Memory Snapshots tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156407674-863dc9f3-c83e-4a31-8ad2-ba9ca25c4bb2.png#averageHue=%23fafafa&clientId=ue6173c60-5ac8-4&from=paste&height=605&id=u6664bc57&originHeight=907&originWidth=802&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=56722&status=done&style=none&taskId=ue6a31103-8800-4214-bd0b-5bb7f3d3819&title=&width=534.6666666666666)
<a name="VtUnt"></a>
## 11.Metadata tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156520550-7a32e296-ea9a-4ad9-b2f3-a6a8433e5520.png#averageHue=%23fafafa&clientId=ue6173c60-5ac8-4&from=paste&height=373&id=ufe9cacc5&originHeight=559&originWidth=1391&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=53282&status=done&style=none&taskId=u933734d4-813a-441d-b993-22e6233cd96&title=&width=927.3333333333334)
<a name="Y6OMx"></a>
## 12.Proto tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156555860-35e052f6-9a56-4f88-b75b-a5167863dd1a.png#averageHue=%23f9f9f9&clientId=ue6173c60-5ac8-4&from=paste&height=248&id=u1a9871a4&originHeight=372&originWidth=601&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=19127&status=done&style=none&taskId=u00d30c6c-435d-4959-a81a-49033fe9234&title=&width=400.6666666666667)
<a name="GMc8f"></a>
## 13.Slice tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156591206-4fec7ac4-15bf-4e8f-8860-88eae10e934e.png#averageHue=%23fafafa&clientId=ue6173c60-5ac8-4&from=paste&height=213&id=uc9bdc4ba&originHeight=320&originWidth=1661&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=42718&status=done&style=none&taskId=ud7a6cc20-69b0-4047-a899-3cb86255d9d&title=&width=1107.3333333333333)
<a name="hGf3z"></a>
## 14.v8 tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156631061-33884a0e-5d66-4cda-8872-7e7b45734e0f.png#averageHue=%23fafafa&clientId=ue6173c60-5ac8-4&from=paste&height=407&id=u6beda93d&originHeight=610&originWidth=1685&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=75707&status=done&style=none&taskId=u045be93d-d077-4749-a03b-6a1a035e53c&title=&width=1123.3333333333333)
<a name="ZBfeE"></a>
## 15.Winscope tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156657554-1939999f-19f9-4158-9cc0-51cb2d1f967c.png#averageHue=%23fdfdfd&clientId=ue6173c60-5ac8-4&from=paste&height=170&id=uf5328aad&originHeight=255&originWidth=1703&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=15664&status=done&style=none&taskId=u1e189f60-e03a-49a6-b646-457c6863a42&title=&width=1135.3333333333333)
<a name="GBPvb"></a>
## 16.Misc tables
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722156691683-998e6db5-ebbb-499c-8dfe-dcbdaae7d192.png#averageHue=%23fbfbfb&clientId=ue6173c60-5ac8-4&from=paste&height=347&id=u7abd80fa&originHeight=521&originWidth=1678&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=46179&status=done&style=none&taskId=u330629f8-f8c9-4cc0-abdf-7e7ed2f96a4&title=&width=1118.6666666666667)

<a name="OtP9b"></a>
# 八、如何通过表之间的关系实现多表联查
<a name="bbkHo"></a>
## 1.确定想要获取数据的类型
假设我们想获得的数据与tracks有关，那我们来观察tracks表视图<br />![](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722149371493-194ecf8a-10d1-4862-a69f-2fa99b1b3f24.png?x-oss-process=image%2Fformat%2Cwebp#averageHue=%23fcfcfc&from=url&id=lOwoV&originHeight=633&originWidth=1763&originalType=binary&ratio=1.5&rotation=0&showTitle=false&status=done&style=none&title=)

<a name="V7uql"></a>
## 2.观察相关表之间的关系
假设我们想要查询的数据与process有关<br />那么我们终点关注名称里面带process的表<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722174086441-c31965bb-0378-47c9-87c7-4da75ab97e8b.png#averageHue=%23dde7ce&clientId=u049f7486-bab9-4&from=paste&height=229&id=u7a11f498&originHeight=343&originWidth=681&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=101845&status=done&style=none&taskId=u6e20d4c0-0665-468b-89e0-355ef5f1633&title=&width=454)<br />不难发现，有两个表process_track与process，他们之间通过upid相连接
<a name="ETQjU"></a>
## 3.观察相关表的结构
<a name="ReUA8"></a>
### process表：
```sql
select * from process
```
我们可以看到具体的表结构，process表还有其他属性，在此不完全展示。<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722174244889-872d482c-1fa8-4d1e-afa6-1cbbfa104ee0.png#averageHue=%23b1b8a1&clientId=u049f7486-bab9-4&from=paste&height=354&id=uaa8ea475&originHeight=531&originWidth=2134&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=99119&status=done&style=none&taskId=u3ff404a1-6f5e-472d-b0c5-b5ee93e82af&title=&width=1422.6666666666667)
<a name="SZObG"></a>
### process_track表：
```sql
select * from process_track
```
我们接着看看process_track表<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722174425010-cd686af6-7500-41fe-abb1-49e6b1b15201.png#averageHue=%23525851&clientId=u049f7486-bab9-4&from=paste&height=421&id=u140dc482&originHeight=632&originWidth=2145&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=132973&status=done&style=none&taskId=u5ff1f9c9-5f7a-4582-9b91-70b364f1179&title=&width=1430)<br />我们可以发现他们之间都有upid属性
<a name="slmVW"></a>
## 4.明确自身需求，进行查询
假设我们想找到cmdline，name ，那么我们可以通过upid进行对表进行关联
```sql
SELECT p.upid, pt.name, p.cmdline
FROM process p
JOIN process_track pt ON p.upid = pt.upid;
```
![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722175066228-47a7c7ee-c246-4d27-8c9d-ba503cb80e46.png#averageHue=%235b6156&clientId=u049f7486-bab9-4&from=paste&height=690&id=u38e567ea&originHeight=1035&originWidth=2123&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=225999&status=done&style=none&taskId=ud5893f49-d185-48e9-8f07-af5e2ee06f0&title=&width=1415.3333333333333)
<a name="fZKxQ"></a>
## 5.添加限制条件进行筛选
我们可以为结果添加一点限制条件来进行筛选
```sql
SELECT p.upid, pt.name, p.cmdline
FROM process p
JOIN process_track pt ON p.upid = pt.upid
WHERE p.cmdline LIKE '%aop%';
```
我们看到需要的结果已经查询成功<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722175269724-fe701c4e-e8a2-48f2-8511-9726b49cbe64.png#averageHue=%2381887a&clientId=u049f7486-bab9-4&from=paste&height=589&id=u258a733c&originHeight=883&originWidth=2071&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=175577&status=done&style=none&taskId=u26e35054-3896-4bc4-b965-a1d425fb1df&title=&width=1380.6666666666667)
<a name="OKUse"></a>
## 6.关系代数角度解释
<a name="mZ77j"></a>
### **1.关系和属性定义**：

- 我们有两个关系（表）`process` 和 `process_track`。
- `process` 表包含属性 `upid`、`name` 和 `cmdline` 等。
- `process_track` 表包含属性 `upid` 和 `name` 等。
<a name="lypoe"></a>
### 2. 选择操作 ：
 首先，对 `process` 和 `process_track` 表分别进行选择操作以展示其表结构：  
```sql
SELECT * FROM process;
SELECT * FROM process_track;
```
 在关系代数中，这可以表示为 `σ(True)(process)` 和 `σ(True)(process_track)`，即选出所有元组。  
<a name="IoV8L"></a>
### **3.连接操作**：  
 为了找到 `cmdline` 和 `name`，我们需要基于 `upid` 属性将两个表关联起来：  
```sql
SELECT p.upid, pt.name, p.cmdline
FROM process p
JOIN process_track pt ON p.upid = pt.upid;
```
 在关系代数中，这相当于自然连接操作：  <br />$\pi_{\text{p.upid, pt.name, p.cmdline}} (\text{process} \bowtie_{\text{process.upid = process\_track.upid}} \text{process\_track})$
<a name="wWp1A"></a>
### 4.**添加限制条件**：  
 为了筛选出 `cmdline` 中包含 "aop" 的数据，我们可以添加一个选择操作：  
```sql
SELECT p.upid, pt.name, p.cmdline
FROM process p
JOIN process_track pt ON p.upid = pt.upid
WHERE p.cmdline LIKE '%aop%';
```
在关系代数中，它是这样表示的：<br />$\pi_{\text{p.upid, pt.name, p.cmdline}} (\sigma_{\text{p.cmdline LIKE \%aop\%}} (\text{process} \bowtie_{\text{process.upid = process\_track.upid}} \text{process\_track}))$

<a name="perfettosql"></a>
# 九、PerfettoSQL 
熟悉了基本查询语法，我们来看看Perfetto为我们提供的库函数
<a name="sWaHS"></a>
## 1.引入PerfettoSQL 模块
例如我们需要引入android.startup.startups模块
```sql
INCLUDE PERFETTO MODULE android.startup.startups;

-- Use the android_startups table defined in the android.startup.startups
-- module.
SELECT *
FROM android_startups;
```
然后执行下面sql语句：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722176397838-c2835095-3ae1-44dd-912e-4eec9e265ee0.png#averageHue=%23737b74&clientId=u049f7486-bab9-4&from=paste&height=250&id=u32d29f22&originHeight=375&originWidth=2120&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=75728&status=done&style=none&taskId=ud0b6387a-81c6-47fc-8e11-eea438637d9&title=&width=1413.3333333333333)我们可以看到查询到了android.startup.startups模块的内容：

<a name="TniHx"></a>
### 引入所有android模块
```sql
-- Include all modules under android/.
INCLUDE PERFETTO MODULE android.*;
```

<a name="HJBvc"></a>
### 引入所有stdlib模块
```sql
-- all stdlib modules:
INCLUDE PERFETTO MODULE *;
```

<a name="shtPI"></a>
## 2.定义函数
假设我们想对其中一个属性进行操作，比如说我把所有的upid减去10：
```sql
-- Include all modules under android.
INCLUDE PERFETTO MODULE android.*;

-- 创建一个函数，将输入的 upid 减去10
CREATE PERFETTO FUNCTION subtract_ten(x INT) RETURNS INT AS SELECT $x - 10;

-- 使用 subtract_ten 函数
SELECT subtract_ten(p.upid) AS upid, pt.name, p.cmdline
FROM process p
JOIN process_track pt ON p.upid = pt.upid
WHERE p.cmdline LIKE '%aop%';
```
运行成功：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722177426241-30fdb5a1-e785-4e49-957a-3cf881d79ceb.png#averageHue=%23808678&clientId=u049f7486-bab9-4&from=paste&height=615&id=u171678ca&originHeight=922&originWidth=2124&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=190905&status=done&style=none&taskId=u54abeb79-3f1a-4029-855a-9bcea412936&title=&width=1416)
<a name="SF0RG"></a>
## 3.不同类型的函数定义示例
<a name="CJdbt"></a>
### 1.创建无参数的标量函数
```sql
CREATE PERFETTO FUNCTION constant_fn() RETURNS INT AS SELECT 1;
```
 这个函数 `constant_fn` 没有参数，返回一个整数值 `1`
<a name="rljA9"></a>
### 2. 创建带有两个参数的标量函数  
```sql
CREATE PERFETTO FUNCTION add(x INT, y INT) RETURNS INT AS SELECT $x + $y;
```
 这个函数 `add` 接受两个整数参数 `x` 和 `y`，返回它们的和。  
<a name="HYa3K"></a>
###  3. 创建无参数的表函数  
```sql
CREATE PERFETTO FUNCTION constant_tab_fn()
RETURNS TABLE(ts LONG, dur LONG) AS
SELECT column1 as ts, column2 as dur
FROM (
  VALUES
  (100, 10),
  (200, 20)
);
```
 这个函数 `constant_tab_fn` 没有参数，返回一个表，表有两列 `ts` 和 `dur`，包含两行数据 `(100, 10)` 和 `(200, 20)`。  
<a name="ntyOs"></a>
###  4. 创建带有一个参数的表函数  
```sql
CREATE PERFETTO FUNCTION sched_by_utid(utid INT)
RETURNS TABLE(ts LONG, dur LONG, utid INT) AS
SELECT ts, dur, utid
FROM sched
WHERE utid = $utid;
```
 这个函数 `sched_by_utid` 接受一个整数参数 `utid`，返回一个表，表有三列 `ts`、`dur` 和 `utid`，包含符合条件 `utid = $utid` 的行。  
<a name="sqa4U"></a>
## 4.定义宏
宏可以将表作为参数传递给 SQL 的“类似函数”的代码段。
<a name="jZDCL"></a>
###  1. 创建无参数的宏  
```sql
CREATE PERFETTO MACRO constant_macro() RETURNS Expr AS (SELECT 1);

-- 使用宏
SELECT constant_macro!();

-- 实际执行的 SQL 是：
-- SELECT (SELECT 1);
```
 这个宏 `constant_macro` 没有参数，返回一个标量表达式 `(SELECT 1)`。  
<a name="X75zc"></a>
###  2. 创建一个无括号的宏  
```sql
CREATE PERFETTO MACRO constant_macro_no_bracket() RETURNS Expr AS 2;

-- 使用宏
SELECT constant_macro_no_bracket!();

-- 实际执行的 SQL 是：
-- SELECT 2;
```
 这个宏 `constant_macro_no_bracket` 没有参数，返回一个标量表达式 `2`。  
<a name="Nh7FE"></a>
###  3. 创建一个带有单个标量参数的宏  
```sql
CREATE PERFETTO MACRO single_arg_macro(x Expr) RETURNS Expr AS (SELECT $x);

-- 使用宏
SELECT constant_macro!() + single_arg_macro!(100);

-- 实际执行的 SQL 是：
-- SELECT (SELECT 1) + (SELECT 100);
```

<a name="pl3zI"></a>
###  4. 创建一个带有表和标量参数的宏  
```sql
-- 创建宏
CREATE PERFETTO MACRO newt_multi_arg_macro(x TableOrSubquery, y Expr)
RETURNS TableOrSubquery AS
(
  SELECT input_tab.input_col + $y
  FROM $x AS input_tab
);

-- 删除现有的表
DROP TABLE IF EXISTS example_table;

-- 使用宏
CREATE TABLE example_table AS
SELECT * FROM newt_multi_arg_macro!((SELECT ts AS input_col FROM sched LIMIT 10), 10);

-- 检查结果
SELECT * FROM example_table;
```
运行结果：<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722178811127-764f052f-905b-4e4e-8775-488116b9bca5.png#averageHue=%23717871&clientId=u9b439043-1843-4&from=paste&height=459&id=ud96b9281&originHeight=689&originWidth=2131&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=114030&status=done&style=none&taskId=u73fce5ca-247f-4135-9a36-3fc5a79b414&title=&width=1420.6666666666667)
<a name="DRF3n"></a>
### 5. 创建一个带有列名参数的宏  
```sql
CREATE PERFETTO MACRO column_macro(col ColumnName) RETURNS Expr AS (SELECT $col);

-- 使用宏
SELECT column_macro!(upid) FROM process;

-- 实际执行的 SQL 是：
-- SELECT (SELECT upid) FROM process;
```
 这个宏 `column_macro` 接受一个列名参数 `col`，返回该列名的值。  <br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/39076974/1722178887697-c7ddbea6-ec18-45c8-bca8-c65228817813.png#averageHue=%2377847a&clientId=u9b439043-1843-4&from=paste&height=515&id=u2b273775&originHeight=773&originWidth=2125&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=80026&status=done&style=none&taskId=u7126210c-1901-427f-b431-034e3b82c89&title=&width=1416.6666666666667)
