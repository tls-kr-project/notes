# Formats

Structured data, as simple as possible.  We will use a combination of YAML and MD for most aspects. As in @kanripo, the files for one text will be in one repository, a GitHub user account for this purpose has been created: @krp-yaml.

Texts will be split into sections, first along *juan* or similar boundaries found in the source. We might decide to have smaller units reflected in the folder layout. We want to have text, annotations and translations as close together as feasible: either in the same folder, or in a structure that mirrors the text layout elsewhere. 


## Text data

 - The format for the text data is YAML. 
 - every text has a toc.yaml file for the table of contents.  At the minimum, it lists all files, if there are sections those are also listed. Metadata for the whole text like title, author, date etc. are also here
 - every text has at least one file named after this pattern: <text_id.NNN> where NNN is the number of the juan. 000 has the frontmatter, prefaces etc. only if available.  For SKQS files there will also be the tiyao. 
 - these numbered files have front, body, back in addition to other metadata props
 - text data that are as such in the source files all are in a property named 'text', with additional parallel props like 'markers', 'punc', 'ent' for standoff markup, pointing to offsets in the text
 - front has role-typed entries:
    - opener: first line(s) of the page (e.g. '欽定四庫全書'); 0 leading full-width spaces
    - title: book title line(s); 1 leading full-width space
    - attribution: author line(s); ≥2 leading full-width spaces
 - first principle: ALL CJK characters go into `text`; everything else — FWS (leading and internal), pb tags, ¶ — goes into `markers`.  This applies uniformly to front, body, and back.
 - body: sequence of sections, each with:
    - id: section identifier
    - head: list with heading text (omitted if no heading)
    - p: sequence of property bundles — [{text: ...}, {markers: [...]}]
 - markers use text_offset pointing into the text string of the same p bundle
 - back: trailing administrative sections (colophons, appendices) in same format as body items

Sample (KR3a0013_001, abbreviated):

```yaml
text_id: KR3a0013
juan: '001'
title: 傅子
date: '2015-08-24'
edition: WYG
juan_title: 傅子
front:
- opener:
  - text: 欽定四庫全書
    markers:
    - {type: pb, text_offset: 0, content: '<pb:KR3a0013_WYG_001-1a>', id: KR3a0013_WYG_001-1a}
    - {type: line, text_offset: 0, content: ¶, id: KR3a0013_WYG_001-1a01}
    - {type: line, text_offset: 6, content: ¶, id: KR3a0013_WYG_001-1a02}
- title:
  - text: 傅子
    markers:
    - {type: fws, text_offset: 0, content: '　', id: ''}
    - {type: line, text_offset: 2, content: ¶, id: KR3a0013_WYG_001-1a03}
- attribution:
  - text: 晉傅𤣥撰
    markers:
    - {type: fws, text_offset: 0, content: '　　　　　　　　　　　　　', id: ''}
    - {type: fws, text_offset: 1, content: '　', id: ''}
    - {type: fws, text_offset: 3, content: '　', id: ''}
    - {type: line, text_offset: 4, content: ¶, id: KR3a0013_WYG_001-1a04}
body:
- id: KR3a0013_001_001
  head:
  - text: 正心篇
  p:
  - text: 立徳之本莫尚乎正心心正而後身正身正而後左右正左右正而後朝廷正朝廷正而後國家正國家正而後天下正故天下不正修之國家國家不正修之朝廷朝廷不正修之左右左右不正修之身身不正修之心所修彌近而所濟彌逺禹湯罪已其興也勃焉正心之謂也心者神明之主萬理之統也動而不失正天地可感而况于人乎况于萬物乎夫有正心必有正徳以正徳臨民猶樹表望影不令而行大雅云儀刑文王萬邦作孚此之謂也有邪心必有枉行以枉行臨民猶樹曲表而望其影之直也若乃身坐廊廟之内意馳雲夢之野情繫曲房之娱臨朝宰事心與體離情與志乖形神且不相保孰左右之正乎忠正仁理存乎心則萬品不失其倫矣禮度儀法存乎體則逺邇内外咸知所則象矣古之君子修身治人先正其心自得而已矣夫能自得則無不得矣苟自失則無不失矣無不得者治天下有餘故否則保身居正終年不失其和達則兼善天下物無不得其所無不失者營妻子不足故否則是已非人而禍逮乎其身達則縱情用物而殃及乎天下昔者有虞氏彈五絃之琴而天下樂其和者自得也秦始皇築長城之基以為固禍機發于左右者自失也夫挾邪心以虐用天下則左右不可保亡秦是也秦之虐君目玩傾城之色天下男女怨曠而不肯恤也耳淫亡國之聲天下大小哀怨而不知撫也意盈四海之外口窮天下之味宫室造天而起萬國為之憔瘁猶未足以逞其欲惟不推心以况人乎故用是人如用草芥使用人如用已惡有不得其性者也古之達治者知心為萬事主動而無節則亂故先正其心其心正于内而後動静不妄動静不妄以率天下而後天下履正而咸保其性也斯逺乎哉求之心而已矣
  - markers:
    - {type: line, text_offset: 21, content: ¶, id: ''}
    - {type: line, text_offset: 42, content: ¶, id: ''}
    - {type: line, text_offset: 63, content: ¶, id: ''}
    - {type: pb, text_offset: 84, content: '<pb:KR3a0013_WYG_001-1b>', id: KR3a0013_WYG_001-1b}
    - {type: line, text_offset: 84, content: ¶, id: KR3a0013_WYG_001-1b01}
    - {type: line, text_offset: 105, content: ¶, id: KR3a0013_WYG_001-1b02}
    - {type: line, text_offset: 126, content: ¶, id: KR3a0013_WYG_001-1b03}
    - {type: line, text_offset: 147, content: ¶, id: KR3a0013_WYG_001-1b04}
    - {type: line, text_offset: 168, content: ¶, id: KR3a0013_WYG_001-1b05}
    - {type: line, text_offset: 189, content: ¶, id: KR3a0013_WYG_001-1b06}
    - {type: line, text_offset: 210, content: ¶, id: KR3a0013_WYG_001-1b07}
    - {type: line, text_offset: 231, content: ¶, id: KR3a0013_WYG_001-1b08}
    - {type: pb, text_offset: 252, content: '<pb:KR3a0013_WYG_001-2a>', id: KR3a0013_WYG_001-2a}
    - {type: line, text_offset: 252, content: ¶, id: KR3a0013_WYG_001-2a01}
    - {type: line, text_offset: 273, content: ¶, id: KR3a0013_WYG_001-2a02}
    - {type: line, text_offset: 294, content: ¶, id: KR3a0013_WYG_001-2a03}
    - {type: line, text_offset: 315, content: ¶, id: KR3a0013_WYG_001-2a04}
    - {type: line, text_offset: 336, content: ¶, id: KR3a0013_WYG_001-2a05}
    - {type: line, text_offset: 357, content: ¶, id: KR3a0013_WYG_001-2a06}
    - {type: line, text_offset: 378, content: ¶, id: KR3a0013_WYG_001-2a07}
    - {type: line, text_offset: 399, content: ¶, id: KR3a0013_WYG_001-2a08}
    - {type: pb, text_offset: 420, content: '<pb:KR3a0013_WYG_001-2b>', id: KR3a0013_WYG_001-2b}
    - {type: line, text_offset: 420, content: ¶, id: KR3a0013_WYG_001-2b01}
    - {type: line, text_offset: 441, content: ¶, id: KR3a0013_WYG_001-2b02}
    - {type: line, text_offset: 462, content: ¶, id: KR3a0013_WYG_001-2b03}
    - {type: line, text_offset: 483, content: ¶, id: KR3a0013_WYG_001-2b04}
    - {type: line, text_offset: 504, content: ¶, id: KR3a0013_WYG_001-2b05}
    - {type: line, text_offset: 525, content: ¶, id: KR3a0013_WYG_001-2b06}
    - {type: line, text_offset: 546, content: ¶, id: KR3a0013_WYG_001-2b07}
    - {type: line, text_offset: 567, content: ¶, id: KR3a0013_WYG_001-2b08}
    - {type: pb, text_offset: 588, content: '<pb:KR3a0013_WYG_001-3a>', id: KR3a0013_WYG_001-3a}
    - {type: line, text_offset: 588, content: ¶, id: KR3a0013_WYG_001-3a01}
- id: KR3a0013_001_002
  head:
  - text: 仁論篇
  p:
  - text: 古之仁人推所好以訓天下而民莫不尚徳...（text continues）...可以及乎逺矣(案此另是一條與上不相屬舊/本惟此數語疑上下尚有脱文)
  - markers:
    - {type: line, text_offset: 21, content: ¶, id: ''}
    - ...
    - {type: pb, text_offset: 285, content: '<pb:KR3a0013_WYG_001-4a>', id: KR3a0013_WYG_001-4a}
    - {type: line, text_offset: 285, content: ¶, id: KR3a0013_WYG_001-4a01}
    - {type: line, text_offset: 306, content: ¶, id: KR3a0013_WYG_001-4a02}
# ... sections 003–024 follow the same pattern (義信篇, 通志篇, ...) ...
- id: KR3a0013_001_025
  head:
  - text: 附録
  p:
  - text: ...
  - markers:
    - ...
```

Example with comment:
```yaml
- id: KR5c0099_001_002_000
  head:
  - text: 第一略標理教
    markers:
    - {type: fws, offset: 0, content: 　　　, id: ''}
  p:
  - comment: 夫大道虚玄言象斯絶理超象繫事出筌蹄非常名之所知豈可道之能究大包無外小入秋毫應現則運於慈舟攝迹則歸於杜默軒轅黄帝齋三月而問之前漢孝文窮數年而不答其體也寂其名也微或駕龍軒而游玉京或控鸞驂而浮金闕西王母得之坐乎少廣東方朔遇之游乎漢庭天地得之以財成群方吹萬而生育重玄至道其大矣哉
    markers:
    - {type: line, offset: 15, content: ¶, id: KR5c0099_HFL_001-001b07}
    - {type: fws, offset: 15, content: 　　　, id: ''}
    - {type: line, offset: 43, content: ¶, id: KR5c0099_HFL_001-001b08}
    - {type: fws, offset: 43, content: 　　　, id: ''}
    - {type: line, offset: 71, content: ¶, id: KR5c0099_HFL_001-001b09}
    - {type: fws, offset: 71, content: 　　　, id: ''}
    - {type: line, offset: 99, content: ¶, id: KR5c0099_HFL_001-001b10}
    - {type: fws, offset: 99, content: 　　　, id: ''}
    - {type: pb, offset: 127, content: '<pb:KR5c0099_HFL_001-002a>', id: KR5c0099_HFL_001-002a}
    - {type: line, offset: 127, content: ¶, id: KR5c0099_HFL_001-002a01}
    - {type: fws, offset: 127, content: 　　　, id: ''}

```


## Notes on key fields

- **text_offset**: character offset into the `text` string of the same `p` bundle.
  Line markers at the very start of a page (after `<pb:>`) share the offset of the
  first character on that page.  Line markers with empty `id` fall before the first
  named page break.
- **front/opener markers**: the `pb` marker and the ¶ that appears on the page-break
  line (id `…1a01`) are both at `text_offset: 0` in the opener item since they
  precede the opener text.
- **attribution text**: `text` contains only CJK characters.  All full-width spaces
  — both the leading indent and the internal separators between dynasty, name, and
  role — appear as `fws` markers at the appropriate `text_offset`.
- **body sections with no header**: if the source has no detected section headers,
  the entire body becomes a single item with no `head` key.
- **back**: trailing sections without a heading (colophons, indexes) are moved to a
  top-level `back` key with the same structure as body items.
