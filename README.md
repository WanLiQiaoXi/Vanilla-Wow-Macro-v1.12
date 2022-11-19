# 魔兽世界 v1.12版本常用宏

[TOC]

## 基础

### 动作条插槽

> 从1.11开始，魔兽世界允许120个动作插槽ID，编号从1到120。1-72由六个默认条使用，73-120被某些职业的状态栏使用。
>
> 从2.3开始，动作槽121-132存在但不可设置：它们指的是默认UI的主要动作条，同时拥有一个目标（[精神控制]、[野兽之眼]、某些任务、卡拉赞的国际象棋事件）。

#### 默认UI的插槽ID

* 动作条第1页：$1-12$
* 动作条第2页：$13-24$
* 动作条第3页（右边动作条）：$25-36$
* 动作条第4页（右边动作条2）：$37-48$
* 动作条第5页（右下方动作条）：$49-60$
* 动作条第6页（左下方动作条）：$61-72$

#### 战士姿态技能条

> 默认都是第1页，按不同姿态切换显示不同技能。

* 战斗姿态技能条：$73-84$
* 防御姿态技能条：$85-96$
* 狂暴姿态技能条：$97-108$

#### 德鲁伊形态技能条

* 猎豹形态技能条：$73-84$
* 潜行形态技能条：$85-96$
* 巨熊形态技能条：$97-108$
* 枭兽形态技能条：$109-120$

#### 盗贼形态技能条

* 潜行形态动作条：$73-84$
* 影舞形态动作条：$85-96$

#### 牧师形态技能条

* 暗影形态动作条：$73-84$

#### 目标控制技能条

* 技能条：$121-132$

## 通用

### 打印技能书

``` lua
--[[
	BOOKTYPE_SPELL = "spell"
--]]
/run local i=1;while true do local spellName,spellRank=GetSpellName(i,BOOKTYPE_SPELL);if not spellName then break;end;DEFAULT_CHAT_FRAME:AddMessage(i..": "..spellName..'('..spellRank..')');i=i+1;end

-- 等价于
/run local i=1;while true do local spellName,spellRank=GetSpellName(i,"spell");if not spellName then break;end;DEFAULT_CHAT_FRAME:AddMessage(i..": "..spellName..'('..spellRank..')');i=i+1;end
```

### 获取技能在技能书的ID

``` lua
/run local GetSpellBookId=function(n,r) local i=1;while 1 do local sn,sr=GetSpellName(i,"spell");if not sn then do break end end;if sn==n and (r==nil or string.find(sr,r)) then return i end;i=i+1;end;end
```

如：

``` lua
-- version 1.12.1
print(GetSpellBookId("恶魔支配"))  -- 52
print(GetSpellBookId("暗影箭",6))  -- 83
```

### 获取玩家buff信息

> 主要是贴图的名字。

``` lua
/run for i=1,32 do local B=UnitBuff("player",i);if B then print(i.."="..B) end;end
```







## 术士

### 死缠+恐惧

``` lua
-- 135是死亡缠绕的技能书ID
/run if GetSpellCooldown(135,"spell")<1 then CastSpellByName("死亡缠绕") else CastSpellByName("恐惧术") end
```

### 蓝胖子牺牲+瞬招蓝胖子

> 需要判定是否已经有蓝胖子的牺牲BUFF，整体写成一条宏则超出256字符了，此时可以：
>
> * 将特定API写入插件来缩短宏长度
> * 或将宏分割成两个，将另一个宏放在不显眼位置供调用就行（为了将API注册到全局）

1. 第1条宏

   ``` lua
   -- 所谓name其实是buff的贴图名字
   /run HasBuff = HasBuff or function(name) for i = 1, 32 do local buffIcon = UnitBuff("player", i); if buffIcon and string.find(buffIcon, name) then return 1 end end return nil end
   ```

2. 核心宏

   ``` lua
   --[[
   	假定1中的HasBuff宏放在第2页的技能条中（就是shift+向下滚轮1下显示的动作条）的第1个插槽
   --]]
   -- 52是恶魔支配技能的技能书ID（需天赋点出）
   /run local c=CastSpellByName;UseAction(13);if UnitCreatureFamily("pet")~="虚空行者" or UnitIsDead("pet")then if GetSpellCooldown(52,"spell")<1 then c("恶魔支配")end c("召唤虚空行者")elseif not HasBuff("SacrificialShield")then c("牺牲")end
   ```



