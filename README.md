# DMIT IP 质量：原生段、风控评分与换 IP 规则，洛杉矶/香港/东京套餐怎么选

搜"DMIT IP 质量"的人，通常不是想听"IP 很干净"这种话。你真正想确认的是三件事：这台机器分到的 IP 到底算不算原生、它会不会被 ChatGPT 或 Netflix 拦下来、以及被墙之后要花多少钱才能换一个。这三个问题的答案分布在三个地方——IP 数据库的检测结果、各站测评的实测截图、以及 DMIT 服务条款里那份写得比多数同行都细的换 IP 规则。

下面按这个顺序拆。

## DMIT 的 IP 在数据库里到底长什么样

先摆结论：DMIT 绝大多数套餐给出的都是**原生 IP**，也就是 IP 注册地和机房实际所在地一致。洛杉矶机器查出来是美国，香港机器查出来是香港，东京机器查出来是日本。

但"原生"只解决了归属地这一层。数据库还会返回另两组字段，这两组才是真正影响你使用体验的东西。

**第一组是使用类型。** 洛杉矶 EB、东京 Pro、香港 Pro 的多份测评里，IPinfo、ipregistry、ipapi、IP2Location、AbuseIPDB 几乎一致地把 DMIT 的 IP 识别为"机房"（Data Center），而不是住宅（ISP）。这点很重要——有人拿"原生 IP"和"住宅 IP"当同义词用，其实不是一回事。机房 IP 在流媒体和风控体系里的权重天然低于住宅 IP。

**第二组是风险评分。** 这一组数据在不同测评里差异相当大，值得单独看：

| 检测项 | 洛杉矶 EB.Pocket | 东京 AS3.Pro.TINY | 香港 AS3.Pro.TINY |
| --- | --- | --- | --- |
| IP2Location | 3（低风险） | 低风险 | 3（低风险） |
| Scamalytics | 0（低风险） | 低风险 | 0（低风险） |
| AbuseIPDB | 0 | 低风险 | — |
| ipapi | 1.42% | 低风险 | 4.30%（标记高风险） |
| IPQS | 75，标记可疑 | 100，标记高风险 | — |
| 黑名单库 | 423 库中 3 个已标记、0 个黑名单 | 423 库中 3 个已标记、0 个黑名单 | 439 库中 80 个已标记、0 个黑名单 |

同一家的机器，在不同风控库里能出现"低风险"和"高风险"并列的情况，这不是测评写错了，而是各家数据库的判定模型本来就不一样。IPQS 的分数普遍偏严，Scamalytics 和 AbuseIPDB 普遍偏松，同一个 IP 在两头跑出完全相反的标签属于常态。

还有个反复出现的细节：MaxMind 的基础坐标字段有时会把 DMIT 的 IP 定位到巴西利亚，而使用地和注册地都返回正确国家。这是数据库字段自身的冲突，不影响流媒体按国家判定，看到了不用慌。

## 同样叫"原生 IP"，差距从哪来

DMIT 的 IP 不是单独采购再广播过来的，洛杉矶段走 AS906、香港段走 AS32519，都是自家 AS 下的地址。这种结构的好处是 IP 的"履历"相对可控，坏处是整段机器的邻居都是同类业务——同一段里挤满了各种代理和落地节点，被哪个平台风控标记过，会连带影响整段的评分。

这一点在社区讨论里说得很直白：有人反馈手里一台正价机的 IP 段因为邻居跑 DDoS 被举报，访问不少站点开始跳验证；也有人同一时期用另一段毫无问题。DMIT 不超售、不混装的承诺解决的是性能和线路问题，解决不了"邻居干什么"这件事。

所以当你看到"DMIT 的 IP 很干净"和"DMIT 的 IP 早被蹬烂了"两种说法同时存在时，两边都可能是真的——取决于具体分到了哪一段。

## 流媒体与 AI 解锁：实测数据里的分歧

这块是搜索量最大、也最容易踩坑的部分。把几份实测结果摊开看：

- 洛杉矶 Eyeball（AN4，EPYC 9654 / 1C1G / 20G）：Netflix 美区、Disney+、YouTube、Amazon Prime、TikTok、Reddit、ChatGPT、Claude 全部按原生方式解锁，三网平均延迟电信 163ms、联通 160ms、移动 156ms。
- 洛杉矶 AS3.EB.Pocket：TikTok、Disney+、Netflix、YouTube、Prime Video、Reddit、ChatGPT 均显示美国地区，原生方式识别。
- 洛杉矶 Pro（AN5 平台）：TikTok、YouTube、Prime Video、Spotify、ChatGPT 解锁，但 Disney+ 不过，Netflix 只能看自制内容。
- 香港 AS3.Pro.TINY：TikTok、Netflix、YouTube、Prime Video 原生解锁，Disney+ 屏蔽、Reddit 屏蔽、ChatGPT 仅 App 可用。
- 东京 AS3.Pro.TINY：TikTok、Disney+、Netflix、YouTube、Prime Video、Reddit、ChatGPT 全部解锁日本区，全部按原生方式识别。

同样是洛杉矶，一台全绿、一台 Disney+ 和 Netflix 打折，说明解锁结果跟产品线、机房、具体 IP 段都有关系，而且会随时间漂移。

> DMIT 服务条款里写得很清楚：分配的 IP 地址不保证特定区域的地理信息，也不保证对特定网站或服务的可用性。这句话不是免责套话，它是理解"DMIT IP 质量"的前提——你买的是网络和硬件，不是解锁承诺。

如果你的核心需求是"必须能看某区 Netflix 某部剧"，任何 VPS 商家都不该被当成解决方案；DMIT 在这件事上的价值是：它的 IP 属于原生段、风控基础分不差，解锁概率高于廉价广播 IP 机器，仅此而已。

## 被墙之后：DMIT 的换 IP 规则写得比同行清楚

这是 DMIT 相对少见的一个优势——它把换 IP 的条件写进了 TOS，并且做成了面板里的自助功能，不用每次开工单扯皮。

**Premium 和 Eyeball 网络（LAX / HKG / TYO 的 Pro 与 EB 系列）**

免费更换需要同时满足四条：

- 实例已购买满 7 天（第 8 天起）
- 距上次更换 IP 已过 15 天（含付费更换记录，多 IP 实例以最近一次为准）
- ICMP 和所有 TCP 端口均被阻断
- 服务剩余有效期至少 7 天

付费更换是 5 美元一次，条件宽松得多：购买满 24 小时、距上次更换满 24 小时、剩余有效期满 24 小时即可。

**Tier 1 网络**

只有付费更换，5 美元一次，间隔要求是 3 天，同样需要购买满 24 小时、剩余有效期满 3 天。

**几条容易被忽略的规则**

- Premium 和 Eyeball 网络有"首次连接保证"：新分配的 IP 必须保证在所有国家可达，Tier 1 不享受这条。
- 如果 ICMP 能 ping 通，或者只是部分国家、部分端口不通，DMIT 按付费处理。
- IP 由系统随机分配，不能指定段或指定 IP。
- 新购 7 天内的实例，换 IP 一律收费。
- 滥用换 IP 功能会被永久关闭该权限。

实际操作路径是客户面板 → 服务详情 → 管理 → 网络选项卡 → 更换 IP。LAX 的 Pro 与 EB 付费更换无需审核，免费更换需要审核；HKG 和 TYO 的 Pro/EB 无论免费还是付费都要走审核；全区域 T1 的付费更换也要审核。审核一般 24 小时内出结果，通过后需要在 48 小时内确认，确认后十分钟左右完成，换完重启实例才生效。

对国内用户来说，15 天一次免费换被墙 IP 的额度，在同类商家里算比较实在的。你可以先通过 👉 [查看 DMIT 在售套餐与当前价格](https://www.dmit.io/aff.php?aff=12046) 确认目标机房的产品线属于哪一档网络，再判断换 IP 政策是否够用。

## 全系套餐参考价对照表

先说明一点：DMIT 的套餐迭代非常频繁。AN4 平台机型已经下架，AN5（AMD EPYC 9005）和 AS3（AMD EPYC 7003）并行在售，同一档套餐在不同平台上价格能差两三美元。下表把多个时间点的补货记录和第三方测评数据做了区间合并，实际金额以下单时的结算页为准。

**美国洛杉矶（AS906）**

| 产品线 / 套餐 | 配置 | 带宽 / 月流量 | 超量后 | 参考价格 | 购买 |
| --- | --- | --- | --- | --- | --- |
| LAX.Pro.TINY | 1 核 / 2G / 20G | 1Gbps / 1TB | 4Mbps 不限流 | $10.9–12.98/月；AN5 另有 $37.99/季起 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.Pro.Pocket | 1–2 核 / 2G / 40G | 4Gbps / 1.5TB | 4Mbps 不限流 | $16.9–18.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.Pro.STARTER | 2 核 / 2G / 80G | 10Gbps / 3TB | 4Mbps 不限流 | $34.9–38.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.Pro.MINI | 2–4 核 / 4G / 80G | 10Gbps / 5TB | 8Mbps 不限流 | $62.9–76.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.Pro.MICRO | 4 核 / 4G / 160G | 10Gbps / 7TB | 8Mbps 不限流 | $87.9–99.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.Pro.MEDIUM | 4–6 核 / 8G / 160G | 10Gbps / 14TB | 10Mbps 不限流 | $199.9–219.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.Pro.LARGE | 8 核 / 16G / 320G | 10Gbps / 25TB | 10Mbps 不限流 | $338.88–459.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.Pro.GIANT | 8–12 核 / 24G / 640G | 10Gbps / 50TB | 10Mbps 不限流 | $619.99–839.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.EB.TINY | 1 核 / 2G / 20G | 2Gbps / 1.5TB | 4Mbps 不限流 | $9.99–12.98/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.EB.Pocket | 2 核 / 2G / 40G | 4Gbps / 3TB | 4Mbps 不限流 | $14.9–18.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.EB.STARTER | 2 核 / 2G / 80G | 10Gbps / 5TB | 4Mbps 不限流 | $29.9–38.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.EB.MINI | 4 核 / 4G / 80G | 10Gbps / 10TB | 8Mbps 不限流 | $58.8–76.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.EB.MICRO | 4 核 / 4G / 160G | 10Gbps / 14TB | 8Mbps 不限流 | $74.99–99.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.EB.MEDIUM | 6 核 / 8G / 160G | 10Gbps / 30TB | 10Mbps 不限流 | $168.88–219.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.EB.Large | 8 核 / 16G / 320G | 10Gbps / 50TB | 10Mbps 不限流 | $338.88/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.EB.GIANT | 12 核 / 24G / 640G | 10Gbps / 100TB | 10Mbps 不限流 | $620–839.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.T1.WEE | 1 核 / 1G / 20G | 10Gbps / 1TB | 100Mbps 不限流 | $36.9/年 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.T1.TINY | 1 核 / 1G / 20G | 10Gbps / 2TB | 200Mbps 不限流 | $6.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.T1.STARTER | 2 核 / 2G / 40G | 10Gbps / 4TB | 200Mbps 不限流 | $12.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.T1.MINI | 2 核 / 4G / 80G | 10Gbps / 8TB | 200Mbps 不限流 | $21.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.T1.MICRO | 4 核 / 4G / 120G | 10Gbps / 16TB | 500Mbps 不限流 | $32.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.T1（AN5 · VOLUME） | 2 核 / 2G 起，最高 12 核 24G | 10Gbps / 5TB–160TB | 100–500Mbps 不限流 | $14.9–199.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| LAX.T1（AN5 · GENERAL） | 2 核 / 4G 起，最高 16 核 32G | 10Gbps / 4TB–32TB | 100–200Mbps 不限流 | $16.9–199.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |

另外还有两个特殊系列：**Premium Secure**（去程走 Cloudflare Magic Transit 清洗、回程保持 CN2 GIA，测试 IP 45.88.194.155）和 **Premium Unmetered**（同样 CN2 GIA，但按固定独享带宽计价、不限流量，起步价就到三位数美元/月）。这两个系列是给被打和高流量业务准备的，跟"IP 干不干净"关系不大。

**香港（AS32519）与东京**

| 产品线 / 套餐 | 配置 | 带宽 / 月流量 | 参考价格 | 购买 |
| --- | --- | --- | --- | --- |
| HKG.Pro.TINY | 1 核 / 1G / 20G | 1Gbps / 500GB | $39.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.Pro.STARTER | 1 核 / 2G / 40G | 1Gbps / 1TB | $79.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.Pro.MINI | 2 核 / 2G / 60G | 1Gbps / 1.5TB | $119.9–149.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.Pro.MICRO | 2–4 核 / 4G / 80–160G | 1Gbps / 2TB | $159.9–179.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.Pro.MEDIUM | 4 核 / 4–8G / 160G | 1Gbps / 2.5TB | $179.9–239.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.Pro.LARGE | 4 核 / 8G / 320G | 1Gbps / 3TB | $240/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.Pro.GIANT | 8 核 / 16G / 640G | 1Gbps / 6TB | $500/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.WEE | 1 核 / 1G / 20G | 4Gbps / 1TB | $36.9/年 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.TINY | 1 核 / 1G / 20G | 4Gbps / 2TB | $6.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.STARTER | 1 核 / 2G / 40G | 10Gbps / 4TB | $12.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.MINI | 2 核 / 2G / 60G | 10Gbps / 8TB | $21.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.MICRO | 4 核 / 4G / 80G | 10Gbps / 16TB | $32.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.MEDIUM | 4 核 / 8G / 160G | 10Gbps / 32TB | $49.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.LARGE | 8 核 / 16G / 320G | 10Gbps / 64TB | $99.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.GIANT | 8 核 / 24G / 640G | 10Gbps / 128TB | $199.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.Pro.TINY | 1 核 / 1G / 20G | 1Gbps / 500GB | $21.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.Pro.STARTER | 1 核 / 2G / 40G | 1Gbps / 1TB | $39.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.Pro.MINI | 2 核 / 2G / 60G | 1Gbps / 2TB | $79.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.Pro.MICRO | 4 核 / 4G / 80G | 1Gbps / 4TB | $159.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.Pro.MEDIUM | 4 核 / 8G / 160G | 1Gbps / 5TB | $259.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.Pro.LARGE | 8 核 / 16G / 320G | 1Gbps / 8TB | $429.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.Pro.GIANT | 8 核 / 24G / 640G | 1Gbps / 15TB | $799.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.T1.TINY | 1 核 / 1G / 20G | 4Gbps / 2TB | $6.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.T1.STARTER | 1 核 / 2G / 40G | 10Gbps / 4TB | $12.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.T1.MINI | 2 核 / 2G / 60G | 10Gbps / 8TB | $21.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.T1.MICRO | 4 核 / 4G / 80G | 10Gbps / 16TB | $32.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.T1.MEDIUM | 4 核 / 8G / 160G | 10Gbps / 32TB | $49.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.T1.LARGE | 8 核 / 16G / 320G | 10Gbps / 64TB | $99.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |
| TYO.T1.GIANT | 8 核 / 24G / 640G | 10Gbps / 128TB | $199.9/月 | [查看该套餐](https://www.dmit.io/aff.php?aff=12046) |

关于香港和东京还有个变动值得知道：第三方测评站记录显示，香港 HKG EBv2 产品线已在 2026 年 8 月 10 日停产停服，存量用户按剩余时间退回账户或原支付网关（超 6 个月的只能退到账户），并附带折扣码用于转购其它服务。EB 系列在港日两个机房的去留比较动荡，下单前务必确认该产品线当前是否仍在售。

## 优惠码：别把第三方整理的码当成现价

DMIT 的优惠码按产品线 × 计费周期 × 活动窗口三段绑定，改版频率比套餐还高。第三方整理的清单里能看到这类形式：

- `2025-XMAS-LAX-PRO-EB-ANNUALLY-STARTER-AND-HIGHER-15OFF-RECURRING` — 洛杉矶 Pro/EB 年付 STARTER 及以上，15% 循环折扣并附返现
- `202510_HKG_TYO_PRO_20OFF_RECURRING` — 香港/东京 Pro，季付及以上 8 折，循环生效
- `202510_HKG_TYO_T1_30OFF_RECURRING` — 香港/东京 T1，季付及以上 7 折，循环生效
- `Lite-Annually-Recur-30OFF` / `Lite-Semi-Annually-Recur-20OFF` — 年付 7 折、半年付 8 折

这些码来自第三方整理页面，能不能用只有结算页说了算。正确做法是在 👉 [DMIT 结算页](https://www.dmit.io/aff.php?aff=12046) 选好套餐和计费周期，把码填进去点 Validate Code 验证，验证通过再付款。别看到"7 折"就直接按折后价算预算。

另外补一句：DMIT 官方页面本身会带自动折扣，比如部分套餐年付直接就是折后价、不需要填码。优惠码和自动折扣不叠加，验证时留意金额变化。

## 按需求挑：谁值得为 IP 质量付溢价

**只想要一个大概率能过 ChatGPT / TikTok 的落地环境。** 洛杉矶 EB 的最低档就够，TINY 一个月十美元出头，原生美区 IP，多个测评的解锁结果是全绿或接近全绿。多花的钱买不到更高的"IP 纯净度保证"——官方明确不做这个保证。

**香港 IP + 国内低延迟 + 还要能过流媒体。** 香港 Pro，但先接受一个事实：香港段的实测里 Disney+ 和 Reddit 是屏蔽的，ChatGPT 只能走 App。香港机器值钱的地方是延迟（华南用户回程能到个位数级别），不是解锁广度。

**做日本区业务或者需要亚洲节点。** 东京 Pro 的实测解锁记录最完整，TikTok、Disney+、Netflix、YouTube、Prime Video、Reddit、ChatGPT 全部日本区原生。代价是入门档 $21.9/月，比洛杉矶同档贵一倍。

**预算紧、只需要国际线路。** T1 系列，年付 $36.9 起。但要清楚 T1 只支持付费换 IP（5 美元、间隔 3 天），没有首次连接保证，IP 被封的成本比 Pro/EB 高。用 T1 跑国内方向的业务，本质上是在赌运气。

**怕被打、或者流量跑不完。** Premium Secure 和 Unmetered 才是对应产品线，跟 IP 质量是两个维度的事。

## 几个常见问题

**Pro 和 EB 的 IP 质量有差别吗？** 从数据库检测结果看，两类产品的使用类型都是机房、风险评分区间高度重叠。差别主要在路由（Pro 走 CN2 GIA 回程，EB 走 CMIN2/9929）和价格，不在 IP 本身。

**能点名要某个 IP 段吗？** 不能。TOS 明确写了 IP 由系统随机分配，不支持指定段或具体地址。换 IP 也一样随机。

**换 IP 之后地理位置会变吗？** 官方不保证。换到的 IP 归属地理论上仍在同一机房，但条款里明确写了"不保证特定区域的任何地理位置信息"。

**IPv6 也走 CN2 GIA 吗？** 各测评说法不一致，有说统一走 CMIN2/9929，有说洛杉矶 Pro 的 IPv6 走 AS4134。实操上把 IPv6 当成国际方向的补充通道更稳妥，别默认它跟 IPv4 的 GIA 同等待遇。

**会退款吗？** DMIT 的退款政策非常严格，多篇第三方测评都提醒"下单即决定"，不要抱有试用后退款的预期。条款里对 CPU 长期满载、25 端口等滥用行为也有明确限制。

**能 ping 通但端口被封，算不算"被墙"？** 不算。免费换 IP 要求 ICMP 和所有 TCP 端口全部不可达，只要还能 ping 通就得走 5 美元的付费通道。
