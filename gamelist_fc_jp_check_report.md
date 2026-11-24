# `gamelist_fc_jp.json` 数据对应关系检查报告

- 检查日期：2026-11-25
- 检查对象：`gamelist_fc_jp.json`（共 **1053** 条）
- 对照数据：
  - No-Intro DAT：`Nintendo - Nintendo Entertainment System (Headered) (20261121-045604).dat`
  - natsugee：1053 个 `natsugee_url` 页面（逐个抓取比对）
  - 维基百科：中文维基百科「红白机游戏列表」源表
  - NESCartDB：`nescartdb_id` 抽查
- 本地 ROM：`rom/` 目录下 1053 个 `.nes` 文件

## 结论摘要

| 检查项 | 结果 |
|---|---|
| No-Intro `nointro_id` / `nointro_rom` / ROM 文件 | **1053/1053 完全正确** |
| natsugee URL / 页面标题 / 图片字段 | **1052/1053 正确**，1 条跨游戏错配 |
| 文本字段跨游戏错配 | **1 条**：`とびだせ大作戦` 实际挂的是 `JJ` 的数据 |
| 图片内容跨游戏复用 | **1 条**：TMNT2 的封面图实际是 TMNT1 封面 |
| 日期差异 | 30 条完整日期不一致，其中 2 条年份/版本级差异 |
| 发行商差异 | 4 条实质性不一致（其余为中日文名称/译名差异） |
| NESCartDB 抽查 | 10 条全部正确（未全量核验） |

**总体判断：拼接结果基本可靠。真正需要修改的是第 281 条 `とびだせ大作戦`，以及第 872 条 TMNT2 的封面图。**

---

## 1. 明确的跨游戏错配：`とびだせ大作戦` 实际是 `JJ` 的数据

**位置：JSON 第 281 条（从 1 开始计数）**

当前记录：

```json
{
  "jp_name": "とびだせ大作戦",
  "cn_name": "3D大作战",
  "en_name": "JJ - Tobidase Daisakusen Part 2",
  "release_date": "1987年12月7日",
  "publisher": "史克威尔",
  "nointro_id": "1107",
  "nointro_rom": "rom/JJ (Japan) (En) (Famicom 3D System).nes",
  "natsugee_url": "https://natsugee.com/famicom/jj/"
}
```

| 字段 | 当前值 | 实际指向 |
|---|---|---|
| `jp_name` | とびだせ大作戦 | 1987-03-12 发售的 **FDS 前作** |
| `cn_name` | 3D大作战 | 同上（前作） |
| `en_name` | JJ - Tobidase Daisakusen Part 2 | **JJ（续作）** |
| `release_date` | 1987年12月7日 | **JJ 的发售日** |
| `publisher` | 史克威尔 | **JJ 的发行商** |
| `nointro_id` | 1107 | DAT 中为 `JJ (Japan) (En) (Famicom 3D System)` |
| `nointro_rom` | `rom/JJ (Japan) (En) (Famicom 3D System).nes` | **JJ 的 ROM** |
| `natsugee_url` | `https://natsugee.com/famicom/jj/` | 页面标题 **ＪＪ**，日期 1987/12/07，厂商 スクウェア |

也就是说：这一行的 **英文名、发售日、发行商、No-Intro ROM、natsugee 页面全部是 `JJ`**，只有日文名和中文名是前作 `とびだせ大作戦`。

### 原因

维基百科源表本身就把两作合并成了一行。中文维基「红白机游戏列表」的原始 wikitext 为：

```text
| JJ - Tobidase Daisakusen Part 2
| {{lang|ja|とびだせ大作戦}}
| 3D大作战
| 1987年12月7日
| 史克威尔
```

而根据日文维基「とびだせ大作戦」条目：

- 前作 `とびだせ大作戦`：FDS，**1987-03-12**，Square / DOG；
- 续作 `JJ 〜 とびだせ大作戦パート2`：卡带（Famicom 3D System），**1987-12-07**，Square。

natsugee 也有独立的 FDS 页面：

- `https://natsugee.com/fds/tobidase-daisakusen/`
  - 标题：とびだせ大作戦
  - 发售日：1987/03/12
  - 厂商：スクウェア

因此这条不是 No-Intro 或 natsugee 拼错，而是维基百科源数据把前作的名字配到了续作 `JJ` 的日期/英文名/厂商上。

### 建议修正

若该行应表示卡带版续作（与 No-Intro、natsugee 一致）：

```json
{
  "jp_name": "JJ 〜 とびだせ大作戦パート2",
  "cn_name": "JJ 大作战2 / 3D大作战2",
  "en_name": "JJ - Tobidase Daisakusen Part 2",
  "release_date": "1987年12月7日",
  "publisher": "史克威尔",
  "nointro_id": "1107",
  "nointro_rom": "rom/JJ (Japan) (En) (Famicom 3D System).nes",
  "natsugee_url": "https://natsugee.com/famicom/jj/"
}
```

若该行本意是 FDS 前作，则整条都应替换为 FDS 数据（FDS ROM、`/fds/tobidase-daisakusen/`、1987-03-12），但它就不应出现在这个以卡带版 No-Intro 为基础的列表中。

---

## 2. 图片跨游戏复用：TMNT2 的封面图实际是 1 代封面

**位置：JSON 第 872 条 `T.M.N.T.2 ザ・マンハッタンプロジェクト`**

- `natsugee_package`：`https://natsugee.com/wp-content/uploads/2025/09/tmnt-2.png`
- 1 代 `T.M.N.T.` 的 `natsugee_package`：`https://natsugee.com/wp-content/uploads/2025/09/teenage-mutant-ninja-turtles.png`
- 两个 URL 下载后的内容 **字节完全相同**，MD5 均为：

```text
ace1916e961feed3b72ab25f97a8e1f5
```

- 图片内容为 **1 代《忍者神龟》封面**：标题只写 `TEENAGE MUTANT NINJA TURTLES`，没有 `2`，也没有 `The Manhattan Project` 字样。
- 但该条的 `natsugee_title` 标题画面是正确的：画面显示 `TEENAGE MUTANT NINJA TURTLES II / ザ・マンハッタン プロジェクト`。

这不是 JSON 拼错 URL，而是 natsugee 源站的两个不同 URL 放了同一张 1 代封面。建议：

1. 替换 `natsugee_package` 为正确的 TMNT2 封面；或
2. 在页面/数据中标注该封面源数据有误。

---

## 3. No-Intro 对应关系：1053/1053 全部正确

逐条验证内容：

1. `nointro_id` 是否存在于 Headered DAT；
2. `nointro_rom` 的文件名是否属于同一个 `<game>` 条目；
3. `rom/` 下文件是否存在；
4. 实际文件 CRC32 与大小是否与 DAT 中 `<rom>` 完全一致；
5. 是否存在重复 `nointro_id` 或重复 `nointro_rom`。

结果：

- 1053 个 `nointro_id` 全部存在；
- 1053 个 `nointro_rom` 全部属于对应 DAT 条目；
- 1053 个 ROM 文件全部存在；
- 1053 个 ROM 的 **CRC32 + 文件大小全部与 DAT 完全一致**；
- 无重复 `nointro_id`；
- 无重复 `nointro_rom`。

补充说明：有 20 条使用了 `World` / `Japan, USA` 等合并版 ROM（例如 `Donkey Kong Jr. (World)`、`Mario Bros. (World)`、`Super Mario Bros. (World)`），但它们与日版是同一游戏，不属于跨游戏错配。

---

## 4. natsugee 对应关系：1052/1053 正常

### 4.1 URL 与页面标题

- 1053 个 `natsugee_url` 全部可访问（HTTP 200）；
- 逐条比对页面 `<h1>` 与 `jp_name`；
- 对字符串相似度较低的 60 条逐条人工复核：
  - 59 条是简称、别名、日文表记差异，例如：
    - `ガンデック` ↔ `GUN-DEC`（英文名 `Vice: Project Doom`）
    - `ダブルドラゴン` ↔ `双截龍`
    - `サラマンダ` ↔ `沙羅曼蛇`
    - `魍魎戦記MADARA` ↔ `摩陀羅`
    - `カラオケスタジオ 専用カセットVol.1` ↔ `カラオケスタジオＶＯＬ.１ トップヒット２０`
  - **1 条真正错配**：第 281 条 `とびだせ大作戦`（见第 1 节）。

### 4.2 图片字段

- 1053 个 `natsugee_title`、`natsugee_package` 与页面自身 JSON-LD / `og:image` 完全一致；
- 1053 个本地图片文件与远程 URL 内容逐字节一致；
- 本地文件 basename 与 URL basename 全部一致；
- 发现 1 处不同 URL 内容相同：TMNT2 封面 = TMNT1 封面（见第 2 节）。

---

## 5. 其他源数据差异（不是跨游戏错配）

### 5.1 日期差异

natsugee 有完整日期的 1023 条中：

- 993 条与 JSON 完全一致；
- 30 条不一致，其中 28 条只差 1–10 天，属于维基百科与 natsugee 的来源差异；
- 2 条是年份/版本级差异，需要单独核对。

#### 需要单独核对的两条

| 游戏 | JSON | natsugee | 说明 |
|---|---|---|---|
| `1943` | 1989年6月20日 | 1988/06/20 | 实际 Famicom 版为 **1988-06-20**，JSON 年份疑似维基笔误 |
| `バイオミラクル ぼくってウパ` | 1988年4月22日 | 1993/02/26 | 1988-04-22 是 **FDS 版**；1993-02-26 是 **卡带版**。该行 ROM 是卡带版，日期更应采用 1993-02-26 |

#### 30 条完整日期差异明细

| # | 游戏（日文名） | JSON | natsugee |
|---|---|---|---|
| 1 | スペランカー | 1985年12月6日 | 1985/12/7 |
| 2 | マグマックス | 1986年3月18日 | 1986/03/19 |
| 3 | セクロス | 1986年5月19日 | 1986/05/15 |
| 4 | 六三四の剣 ただいま修行中 | 1986年8月8日 | 1986/08/06 |
| 5 | 聖飢魔II 悪魔の逆襲 | 1986年12月24日 | 1986/12/25 |
| 6 | 覇邪の封印 | 1987年10月13日 | 1987/10/23 |
| 7 | アウトランダーズ | 1987年12月4日 | 1987/02/04 |
| 8 | マグナム危機一髪 エンパイアシティ1931 | 1987年12月15日 | 1987/12/25 |
| 9 | スーパーマン | 1987年12月22日 | 1987/12/26 |
| 10 | スペースシャドー | 1989年2月15日 | 1989/02/20 |
| 11 | 1943 | 1989年6月20日 | 1988/06/20 |
| 12 | 新・燃えろ!!プロ野球 | 1989年7月27日 | 1989/07/13 |
| 13 | 明治維新 | 1989年9月29日 | 1989/09/26 |
| 14 | 御存知弥次喜多珍道中 | 1989年11月17日 | 1989/11/07 |
| 15 | 激突四駆バトル | 1989年11月27日 | 1989/11/17 |
| 16 | 名門!多古西応援団 | 1989年12月1日 | 1989/12/23 |
| 17 | 井崎脩五郎の競馬必勝学 | 1990年3月20日 | 1990/03/30 |
| 18 | ファイナルファンタジーIII | 1990年4月27日 | 1990/04/24 |
| 19 | 水滸伝・天命の誓い | 1990年6月25日 | 1990/06/29 |
| 20 | インセクターX | 1990年9月20日 | 1990/09/21 |
| 21 | バトルスタジアム 選抜プロ野球 | 1990年12月19日 | 1990/12/20 |
| 22 | なんてったって!!ベースボール 子ガメカセットOBオールスター編 | 1991年2月26日 | 1991/02/28 |
| 23 | SD刑事ブレイダー | 1991年7月19日 | 1991/08/02 |
| 24 | ちびまる子ちゃん うきうきショッピング | 1991年10月4日 | 1991/10/05 |
| 25 | パチンコ大作戦2 | 1992年7月19日 | 1992/07/10 |
| 26 | 囲碁指南'93 | 1992年11月20日 | 1992/11/27 |
| 27 | ドラゴンボールZ 激闘天下一武道会 | 1992年12月29日 | 1992/12/19 |
| 28 | バイオミラクル ぼくってウパ | 1988年4月22日 | 1993/02/26 |
| 29 | FC原人 | 1993年7月30日 | 1993/07/20 |
| 30 | 熱血!すとりーとバスケット | 1993年12月22日 | 1993/12/17 |

> 注：`アウトランダーズ` 的 JSON 为 1987-12-04，natsugee 为 1987-02-04，相差近 10 个月，建议优先核对。

### 5.2 发行商差异

维基百科的 `publisher` 是中文译名，natsugee 是日文原名，不能逐字比较。按“中文名 → 日文厂商”映射后，绝大多数一致，有 4 处实质性不一致：

| 游戏 | JSON 发行商 | natsugee 发行商 |
|---|---|---|
| `ゲゲゲの鬼太郎 妖怪大魔境` | Jaleco | バンダイ |
| `ジャストブリード` | 史克威尔 | エニックス |
| `囲碁名鑑` | 青木书店 | ヘクト |
| `マジックキャンドル` | Sammy | ヘクト |

这些是厂商字段的源数据差异，游戏本身仍对得上，不是“挂了另一款游戏的数据”。

其他大量差异只是名称写法不同，例如：

- `南梦宫` ↔ `ナムコ`
- `科乐美` ↔ `コナミ`
- `Hudson Soft` ↔ `ハドソン`
- `Sun电子` ↔ `サン電子` / `サンソフト`
- `HAL研究所` ↔ `HAL研究所` / `ＨＡＬ研究所`

---

## 6. NESCartDB ID 抽查

`nescartdb_id` 不是本次三方拼接的核心字段，但做了 10 条抽查：

| ID | JSON 英文名 | NESCartDB 页面标题 |
|---|---|---|
| 3667 | Donkey Kong | Donkey Kong |
| 1392 | Popeye | Popeye |
| 1652 | Gomoku Narabe Renju | Gomoku Narabe Renju |
| 1466 | Mahjong | Mahjong |
| 1491 | Mario Bros. | Mario Bros. |
| 3867 | Popeye no Eigo Asobi | Popeye no Eigo Asobi |
| 1449 | Baseball | Baseball |
| 3468 | Donkey Kong Jr. Math | Donkey Kong Jr. no Sansuu Asobi |
| 1455 | Tennis | Tennis |
| 1459 | Pinball | Pinball |

抽查结果全部正确。另有 160 条 `nescartdb_id` 为 `null`，属于缺失值而非错配。

---

## 7. 建议修改清单

### 必须修改

1. **第 281 条 `とびだせ大作戦`**
   - 将 `jp_name` 改为 `JJ 〜 とびだせ大作戦パート2`（或简写 `JJ`）；
   - 将 `cn_name` 改为 `JJ 大作战2` / `3D大作战2`；
   - 保持 `en_name`、`release_date`、`publisher`、`nointro_id`、`nointro_rom`、`natsugee_url` 不变（它们已经是 `JJ` 的数据）。

2. **第 872 条 TMNT2 封面图**
   - 替换 `natsugee_package` 为正确的 TMNT2 封面，或标注源数据有误。

### 建议核对

3. `1943`：年份应为 1988 还是 1989；
4. `バイオミラクル ぼくってウパ`：日期应采用 FDS 版 1988-04-22 还是卡带版 1993-02-26；
5. `アウトランダーズ`：1987-12-04 与 1987-02-04 相差近 10 个月；
6. 上述 4 条发行商差异。

### 无需修改

- No-Intro 的 1053 条 ID / ROM / CRC 对应关系全部正确；
- natsugee 的 URL 和图片字段除 TMNT2 封面外全部正确；
- 其余 1051 条未发现“游戏 A 挂游戏 B 数据”的情况。

---

## 附录：检查方法

1. 解析 `gamelist_fc_jp.json`，确认共 1053 条、字段完整。
2. 解析 Headered No-Intro DAT，建立 `id → <game>` 映射。
3. 逐条检查：
   - `nointro_id` 是否存在；
   - `nointro_rom` basename 是否属于同一 `<game>`；
   - ROM 文件是否存在；
   - CRC32 与 size 是否与 DAT 一致。
4. 抓取全部 1053 个 natsugee 页面，提取：
   - `<h1>`；
   - `発売日` / `メーカー`；
   - JSON-LD 的 package / screenshot 图片；
   - `og:image`。
5. 比较 `jp_name` ↔ natsugee `<h1>`、`release_date` ↔ natsugee 日期、`publisher` ↔ natsugee 厂商。
6. 下载全部 natsugee 封面/标题图，与本地 `image/package`、`image/title` 逐字节比对。
7. 检查图片重复：MD5 完全重复 + dHash 近似重复，人工查看可疑项。
8. 抽查 NESCartDB 页面标题。
9. 对低相似度的标题、日期、厂商逐条人工复核，排除简称/别名/表记差异。
