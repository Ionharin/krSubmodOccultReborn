# KR UKR 社会民主主义路线：理想值与合作机制整理

本文整理 Kaiserreich 中乌克兰共和国社民路线，也就是 `UKR_solidarity_and_compromise` 后的 USDRP 机制。用途是作为后续仿制一套类似国策树/路线机制的设计参考。

## 源文件

- 国策树：`E:\SteamLibrary\steamapps\workshop\content\394360\1521695605\common\national_focus\UKR focus (Ukraine).txt`
- 事件：`E:\SteamLibrary\steamapps\workshop\content\394360\1521695605\events\UKR events (Ukraine).txt`
- 决议：`E:\SteamLibrary\steamapps\workshop\content\394360\1521695605\common\decisions\UKR decisions (Ukraine).txt`
- scripted effects：`E:\SteamLibrary\steamapps\workshop\content\394360\1521695605\common\scripted_effects\UKR effects (Ukraine).txt`
- scripted triggers：`E:\SteamLibrary\steamapps\workshop\content\394360\1521695605\common\scripted_triggers\UKR triggers (ukraine).txt`
- dynamic modifiers：`E:\SteamLibrary\steamapps\workshop\content\394360\1521695605\common\dynamic_modifiers\UKR dynamic_modifiers (Ukraine).txt`
- 英文本地化：`E:\SteamLibrary\steamapps\workshop\content\394360\1521695605\localisation\english\KR_country_specific\UKR - Ukraine l_english.yml`

## 总体结构

`UKR_solidarity_and_compromise` 是共和国政治路线的枢纽国策，本身 `cost = 0`、`dynamic = yes`、`available = { always = no }`，通常由事件或脚本效果自动完成。根据当前政府/领导人不同，它显示为不同名称：

- 默认/选举过渡：`Solidarity and Compromise`
- 社自 URDP：`A Radical Touch`
- 社民 USDRP：`Ascension of the Workers' Party`

社民 USDRP 路线的核心变量是：

```hoi4
UKR_usdrp_idealism_points
```

本地化说明中写明：理想值范围为 `-4` 到 `+6`，但国策奖励加成通常封顶按 `+4` 计算。实际脚本中多数强化公式都会先把变量 clamp 到 `0-4`。

## 理想值基础效果

理想值变化统一通过临时变量 `UKR_idealism_change` 和 scripted effect `UKR_idealism_influence` 实现：

```hoi4
set_temp_variable = { UKR_idealism_change = X }
UKR_idealism_influence = yes
```

`UKR_idealism_influence` 的行为：

- 仅在 `UKR_has_any_failstate = no` 时生效。
- 显示 `UKR_add_idealism_tt`，即“Change in Idealism: X”。
- `add_to_variable = { UKR_usdrp_idealism_points = UKR_idealism_change }`
- 将 `UKR_usdrp_idealism_points` 限制在 `-4` 到 `6`。
- 如果理想值跌到 `-4` 且玩家不是 AI，则 2 天后触发 `ukraine_republican_events.214`，进入“理想背叛”失败状态。

理想值档位触发器：

| 触发器 | 条件 | 用途 |
| --- | --- | --- |
| `UKR_USDRP_idealism_0` | `-4` 到 `0` | 最低/基础档 |
| `UKR_USDRP_idealism_1` | 等于 `1` | 一档 |
| `UKR_USDRP_idealism_2` | 等于 `2` | 二档 |
| `UKR_USDRP_idealism_3` | 等于 `3` | 三档 |
| `UKR_USDRP_idealism_4` | 大于 `3` | 四档及以上，实际加成封顶档 |

## 国策提供的理想值变化

下表只列出直接改变 `UKR_usdrp_idealism_points` 的社民路线国策。很多国策还会“消耗”理想值换强化奖励，见下一节。

| 国策 ID | 英文名 | 理想值变化 | 主要条件/说明 |
| --- | --- | ---: | --- |
| `UKR_strike_down_right_wing_extremists` | Strike Down Right Wing Extremists | `+1` | 社民分支中走“打击右翼极端派”；同时恶化联盟伙伴关系 `-2`。 |
| `UKR_respect_judicial_independence` | Respect Judicial Independence | `-1` | 社民分支中尊重司法独立；同时改善联盟伙伴关系 `+3`，并强化 URDP 合作效果。 |
| `UKR_emergency_powers_to_the_general_court` | Emergency Powers to the General Court | `+1` | 社民分支中扩大总法院紧急权；同时恶化联盟伙伴关系 `-2`。 |
| `UKR_decentralized_national_governance` | Decentralised National Governance | `-1` | 社民分支中去中心化治理；同时改善联盟伙伴关系 `+2`，并强化 URDP 合作效果。 |
| `UKR_municipalize_private_institutions` | Municipalise Private Institutions | `+1` | 社民分支选择市政化私营机构。 |
| `UKR_the_path_of_parliamentary_reformism` | Path of Parliamentary Reformism | `+1` | 若理想值在 0-2 档，直接获得；若已在 3-4 档，则通过 tooltip 分支展示相同效果。该段 KR 原文中有一处疑似变量名笔误：`UKR_emphasise_commitment_to_principles = -25`。 |
| `UKR_local_land_councils` | Local Land Councils | `+1` | 社民、非 Vynnychenko、非革命集团崩溃时。 |
| `UKR_education_for_all` | Education for All | `+1` | 社民、非 Vynnychenko、无失败状态；同时延长党内怀疑任务 35 天。 |

## 消耗理想值的国策

`UKR_idealism_spend` 会显示“Taking this focus will spend an Idealism point!”并在 1 小时后触发隐藏事件 `ukraine_republican_events.180`，效果等价于理想值 `-1`。

这些国策并非都“提供理想值变化”，而是通常按当前理想值放大奖励，然后消耗 1 点理想值：

| 国策 ID | 英文名 | 消耗 | 理想值如何参与奖励 |
| --- | --- | ---: | --- |
| `UKR_a_tight_rein_on_radical_leftists` | A Tight Rein on Radical Leftists | `-1` | 每点理想值额外 `+25` 政治点。 |
| `UKR_centralised_security_apparatus` | Centralised Security Apparatus | `-1` | 稳定度奖励公式约为 `0.05 + 理想值*0.03`。 |
| `UKR_delegate_control_over_peoples_militia` | Delegate Control over People's Militia | `-1` | 根据理想值档位给予不同版本民族精神。 |
| `UKR_break_regional_patronage_networks` | Break Regional Patronage Networks | `-1` | 社民支持度奖励为 `(理想值0-4 + 1) * 0.02`。 |
| `UKR_controlled_capitalism` | Controlled Capitalism | `-1` | 每点理想值额外 `+15` 政治点，并强化 URDP 合作动态修正。 |
| `UKR_merger_of_monopolized_industries` | Merger of Monopolized Industries | `-1` | 改革分数/工业分数为 `3 + 理想值*2`。 |
| `UKR_land_fund_establishment` | Land Fund Establishment | `-1` | 根据理想值档位给予不同版本土地基金民族精神。 |
| `UKR_promote_grassroots_cultural_initiatives` | Promote Grassroots Cultural Initiatives | `-1` | 根据理想值档位给予不同版本 `Prosvita` 民族精神。 |
| `UKR_support_for_nruz` | Support for NRUZ | `-1` | 强化 URDP 合作动态修正，工厂产出加成随理想值增加。 |
| `UKR_overtures_to_vienna` | Overtures to Vienna | `-1` | 根据理想值强化民族精神，常见文本为建造速度/科研速度每点 `+3%`。 |
| `UKR_beyond_mitteleuropa` | Beyond Mitteleuropa | `-1` | 根据理想值强化政治点、建造速度、消费品、贸易关系等变量。 |
| `UKR_progressive_tax_policy` | Progressive Tax Policy | `-1` | 工业分数为 `3 + 理想值*2`。 |
| `UKR_bolster_the_utsentrprof` | Bolster the Utsentrprof | `-1` | 根据理想值档位给予民族精神/强化奖励。 |
| `UKR_true_peoples_army` | True People's Army | `-1` | 根据理想值档位给予人民军队民族精神。 |
| `UKR_prosperity_under_state_control` | Prosperity under State Control | `-1` | 强化 URDP 合作动态修正，生产效率起始值随理想值提升。 |
| `UKR_our_own_industrial_revolution` | Our Own Industrial Revolution | `-1` | 工业项目成本减少/完成时间缩短，公式分别与 `(1 + 理想值)` 相关。 |
| `UKR_introduce_worker_self_management` | Introduce Worker Self-Management | `-1` | 工人控制等级取当前理想值，影响后续民族精神。 |
| `UKR_create_the_presidential_office` | Create the Presidential Office | `-1` | 领导人/内阁相关加成按理想值提高。 |

## 事件选项提供的理想值变化

下表列出事件中直接改变理想值的选项。事件名和选项名使用 KR 英文本地化，方便回查。

| 事件 ID | 事件名 | 选项 ID | 选项文本 | 理想值变化 | 备注 |
| --- | --- | --- | --- | ---: | --- |
| `ukraine_republican_events.34` | Pragmatism versus Radicalism | `.34.b2` | A great transformation of Ukrainian society is upon us! | `+2` | USDRP 选项；政治点 `-40`，联盟伙伴关系 `-2`。 |
| `ukraine_republican_events.59` | A Menace from the Left | `.59.a` | We must defend our democracy against all threats. | `+1` | 社民时触发；社自则改 URDP 派系影响。 |
| `ukraine_republican_events.151` | A Lone Shot | `.151.a` | We must be vigilant and thorough. | `+2` | 社民时触发；陆军经验 `-45`。 |
| `ukraine_republican_events.155` | The Wealth of the People | `.155.a` | Perhaps a slight delay is in order. | `-1` | 社民时触发；改善联盟伙伴关系 `+2`。 |
| `ukraine_republican_events.173` | Lviv Riots | `.173.a` | No leniency towards the national chauvinists. | `+1` | 社民时政治点 `-50`。 |
| `ukraine_republican_events.175` | Land Reform Budget Shortages | `.175.a2` | Provide Martos with the necessary funds. | `+1` | 社民专属选项；政治点 `-75`，改革分数 `+4`。 |
| `ukraine_republican_events.180` | 隐藏调试/消耗事件 | option 1 | 无文本 | `-1` | 被 `UKR_idealism_spend` 调用，用于国策消耗理想值。 |
| `ukraine_republican_events.187` | Eyes to the Left | `.187.a` | Let's send Margolin to Paris. | `-1` | 改善联盟伙伴关系 `+2`。 |
| `ukraine_republican_events.187` | Eyes to the Left | `.187.b` | Send Yeremiiv instead! | `+1` | 恶化联盟伙伴关系 `-2`。 |
| `ukraine_republican_events.190` | Wrath of the Puppetmaster | `.190.a` | Reality is a harsh mistress... | `-1` | 稳定度 `-10%`，联盟伙伴关系 `-2`。 |
| `ukraine_republican_events.193` | The Split | `.193.a` | An old rift between the reformists and the revolutionaries. | `+2` | 社民路线早期党内分裂事件。 |
| `ukraine_republican_events.206` | Expanding the Rural Outreach | `.206.a` | The USDRP is meant to be the main party of the working masses. | `+1` | 政治点 `-50`。 |
| `ukraine_republican_events.207` | The Radical Faction Prevails | `.207.a` | So be it. | `-1` | 社民支持度 `-5%`，后续触发 `.209`。 |
| `ukraine_republican_events.208` | Clash of the Socialist-Revolutionaries | `.208.a` | Good news! | `+1` | 社民支持度 `+5%`。 |
| `ukraine_republican_events.209` | Limiting the Damage | `.209.b` | No need to make the opposition even more hostile. | `+1` | 稳定度 `+2%`，社民支持度 `-5%`，激社支持度 `+10%`。 |
| `ukraine_republican_events.212` | In Defence of the Republic | `.212.a` | No need to be stingy. | `+2` | 联盟伙伴关系 `-3`，改革/工业分数各 `-5`。 |
| `ukraine_republican_events.213` | Petliura's Vartovyi | `.213.a` | A dangerous man, but a useful one. | `+1` | 党内怀疑任务剩余时间 `-30` 天，联盟伙伴关系 `-3`。 |
| `ukraine_republican_events.214` | Ideals Betrayed | `.214.a` | 失败状态选项 | 重置为 `0` | 当理想值到 `-4` 时触发，设 `UKR_idealism_failstate`，移除维护决议/任务。 |
| `ukraine_republican_events.215` | The Trust Compromised | `.215.b` | Enough is enough. Lytvynenko will get the job. | `+1` | 政治点 `-35`，社民支持度 `-2%`。 |
| `ukraine_republican_events.217` | Courting the Unions | `.217.a` | A great day for the labour movement! | `+1` | 若已完成 `UKR_bolster_the_utsentrprof`，额外社民支持/政治点。 |
| `ukraine_republican_events.220` | The Power of a Word | `.220.b` | A new course for new circumstances. | `+1` | 政治点 `-35`，稳定度 `-3%`。 |
| `ukraine_republican_events.222` | The Resistance of the Old Order | `.222.a` | We can wait out their futile resistance. | `+1` | 党内怀疑任务剩余时间 `-30` 天。 |
| `ukraine_republican_events.223` | The Reorganisation of the Army Inspectorate | `.223.a` | No harm in ruffling some feathers, appoint Udovychenko. | `+1` | 联盟伙伴关系 `-3`。 |
| `ukraine_republican_events.224` | 隐藏“补理想值”事件 | option 1 | 无文本 | `+1` | `UKR_add_idealism` 调用，“just in case”。 |
| `ukraine_republican_events.225` | Drahomanov University Event | `.225.a` | The main cause – political, social and cultural progress for an individual and a community. | `+1` | 非 Vynnychenko 时生效。 |
| `ukraine_republican_events.227` | Looking Towards the Future | `.227.b` | We need a disciplined youth wing. | `+1` | 与 `.227.a` 的直接社民支持度奖励互斥。 |

## 决议与任务中的理想值变化

`UKR_scepticism_within_the_party`：

- 是一个不可手动选择的负面任务，`days_mission_timeout = UKR_scepticism_days_timeout`。
- 超时后会重新激活自身，并执行理想值 `-1`。
- 如果理想值已经低于 `-1`，隐藏触发 `ukraine_republican_events.262`。
- 多个国策/事件会增加或减少这个任务的剩余时间，表现为党内理想主义压力。

`UKR_emphasise_commitment_to_principles`：

- targeted decision，由 `ukraine_republican_events.192` 激活。
- 花费 `UKR_principles_cost`，初始设为 `75`。
- 完成时理想值 `+1`。
- 最多使用 3 次；每次 remove 时 `UKR_commitment_var +1`，到 3 后移除该 targeted decision。
- 部分国策会降低 `UKR_principles_cost`，鼓励玩家主动维护理想值。

## 合作机制

合作机制的核心变量是：

```hoi4
UKR_coalition_partner_attitude
```

路线初始化时多处将其设为 `10`，即中立/冷淡区间。它的范围是 `0-20`，四档如下：

| 档位触发器 | 分数范围 | 本地化含义 |
| --- | ---: | --- |
| `UKR_coalition_partner_uncooperative` | `0-5` | Uncooperative |
| `UKR_coalition_partner_indifferent` | `6-10` | Indifferent |
| `UKR_coalition_partner_cooperative` | `11-15` | Cooperative |
| `UKR_coalition_partner_amenable` | `16-20` | Amenable |

国策和事件不直接写死加减 `UKR_coalition_partner_attitude`，而是先设置：

```hoi4
set_temp_variable = { UKR_coalition_partner_attitude_change = X }
UKR_coalition_partner_relationship_improve = yes
```

或：

```hoi4
set_temp_variable = { UKR_coalition_partner_attitude_change = -X }
UKR_coalition_partner_relationship_worsen = yes
```

这两个 effect 实际都会：

- 检查没有 `UKR_minority_government`。
- 根据当前执政党显示不同 tooltip。
- 将变化量加到 `UKR_coalition_partner_attitude`。
- clamp 到 `0-20`。
- 调用 `UKR_coalition_partner_attitude_modifiers` 刷新动态修正。

如果关系跌入 `Uncooperative`，且不在选举任务中，会激活联盟伙伴离开执政联盟的任务：

- 社自执政时激活 `UKR_usdrp_leave_coalition`。
- 社民执政时激活 `UKR_urdp_leave_coalition`。

## 合作关系的动态修正

四档关系各对应一个 dynamic modifier：

- `UKR_coalition_partner_uncooperative`
- `UKR_coalition_partner_indifferent`
- `UKR_coalition_partner_cooperative`
- `UKR_coalition_partner_amenable`

这些 modifier 不写固定数值，而是从变量读取：

```hoi4
political_power_gain = UKR.UKR_coalition_partner_*_political_power_gain
stability_factor = UKR.UKR_coalition_partner_*_stability_factor
war_support_factor = UKR.UKR_coalition_partner_*_war_support_factor
production_speed_buildings_factor = UKR.UKR_coalition_partner_*_production_speed_buildings_factor
industrial_capacity_factory = UKR.UKR_coalition_partner_*_industrial_capacity_factory
```

社民路线中，若一个国策“照顾/强化 URDP 合作效果”，常见写法是直接给四档的变量加值，例如：

```hoi4
add_to_variable = { UKR.UKR_coalition_partner_indifferent_industrial_capacity_factory = 0.01 }
add_to_variable = { UKR.UKR_coalition_partner_cooperative_industrial_capacity_factory = 0.03 }
add_to_variable = { UKR.UKR_coalition_partner_amenable_industrial_capacity_factory = 0.05 }
```

这意味着合作机制既有“当前关系等级”这一层，又有“每个等级能提供什么收益”这一层。后者可以被国策永久改写。

## 设计观察

KR 这套系统的核心张力是：

- 理想值越高，社民政治国策奖励越强。
- 很多强奖励国策会消耗 1 点理想值，形成“储备资源”玩法。
- 强硬/原则性选择通常增加理想值，但经常恶化联盟伙伴关系、消耗政治点、降低稳定度或改革分数。
- 妥协/务实选择通常改善联盟伙伴关系，甚至给政治点/稳定度，但会降低理想值或放弃理想值增长。
- 合作关系本身也能提供动态国家加成，且可被国策永久强化。
- 党内怀疑任务会周期性消耗理想值，迫使玩家持续维护路线合法性。
- 理想值到 `-4` 触发失败状态，使系统有明确下限惩罚。

如果仿制，可以抽象成三层：

1. 路线资源：如“理想值/原则性/纯洁性”，范围可设为 `-4` 到 `6` 或 `0` 到 `100`。
2. 伙伴关系：如“联盟伙伴态度”，范围 `0-20`，分四档动态修正。
3. 维护压力：周期性任务或事件，若不投入资源就降低路线资源，跌破阈值触发失败/裂党。

