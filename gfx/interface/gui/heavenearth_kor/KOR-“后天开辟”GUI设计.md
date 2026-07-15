核心目标：模拟KX中的“中欧同盟”功能，只是其内容只针对朝鲜半岛领土和随着后续游戏推进解锁的日本本岛。

GUI名称：后天开辟（Heaven Earth）

为完成本目标，将分为三个阶段推进构建。

第一阶段：GUI外壳

游戏内可用限制：只有has_cosmetic_tag = KOR_pnr_pocheongyo / KOR_pnr_taegeukdo / KOR_pnr_jeungsanbeop的国家能够看到并打开此GUI按钮，原始国家tag 为KOR的国家仅能看到此GUI按钮但无法打开。其它国家无法看到此GUI按钮。

外部按钮与KR原版中GER的“中欧同盟”相同，挂在主界面的raid_filter锚点附近。在点击按钮，打开GUI后主体会显示和KX版本相同的GUI地图，但仅有一张韩国本岛：
1.右上角为退出按钮
2.上方居中白色标题为“后天开辟”。
3.地图下方居中显示提示性白色字体文本“使用箭头导航各地区，点击其省份即可查看经济项目。”
4.左下角显示“开辟点数”，随后换行“等级：”后面为绿色变量数字；右下角显示“开辟等级”，随后换行“等级：X / 5”，X为另一个绿色变量数字。
5.最下方横向排列有四个按钮，分别有可选和不可选两种贴图，默认展示可选贴图。

相关GUI图片素材地址如下：

打开后的GUI框架：暂时复用 KR 版本的中欧同盟
挂载在主界面的外部按钮："C:\Users\PC\Documents\Paradox Interactive\Hearts of Iron IV\mod\KR_developing_penglaiNondumRepetra\gfx\interface\gui\heavenearth_kor\button_kor_heaven_earth.png"
打开GUI后显示的韩国地图："C:\Users\PC\Documents\Paradox Interactive\Hearts of Iron IV\mod\KR_developing_penglaiNondumRepetra\gfx\interface\gui\heavenearth_kor\korean_map.png"
横排四个按钮（从左到右）：
按钮1："C:\Users\PC\Documents\Paradox Interactive\Hearts of Iron IV\mod\KR_developing_penglaiNondumRepetra\gfx\interface\gui\heavenearth_kor\new_factory_select.png"
按钮2："C:\Users\PC\Documents\Paradox Interactive\Hearts of Iron IV\mod\KR_developing_penglaiNondumRepetra\gfx\interface\gui\heavenearth_kor\max_factory_select.png"
按钮3："C:\Users\PC\Documents\Paradox Interactive\Hearts of Iron IV\mod\KR_developing_penglaiNondumRepetra\gfx\interface\gui\heavenearth_kor\industry_speed_select.png"
按钮4："C:\Users\PC\Documents\Paradox Interactive\Hearts of Iron IV\mod\KR_developing_penglaiNondumRepetra\gfx\interface\gui\heavenearth_kor\research_bonus_select.png"



第二阶段：GUI交互

1.地图上的韩国整体可以进行交互，类似于KX中的“中欧同盟”，四个省份都可以被单独点击。在点击选中之后，就可以选择GUI下方的四个选项，鼠标放在选项上会显示悬浮窗，展示其选项名称及选择后效果，每个选项都只能选择一次，可以消耗开辟点数对对应的省份进行选项升级。升级完成后切换为不可选模式。
2.每个省份都可以进行4次升级，在四次升级之后省份贴图会切换为“完成版”，并让“开辟等级”+1。

相关贴图位置：C:\Users\PC\Documents\Paradox Interactive\Hearts of Iron IV\mod\KR_developing_penglaiNondumRepetra\gfx\interface\gui\heavenearth_kor中的korean_zone_1到korean_zone_4

第三阶段：具体数值

第三阶段内容后续商定。
