---
title: florr.oi
published: 2026-01-07
description: "v7.0"
image: "./cover.jpeg"
tags: ["HTML"]
category: 网页
draft: false
---

```html
<!DOCTYPE html>
<!--floor.oi - v7.0-->
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>floor.oi - 生态深渊</title>
<style>
* { margin:0; padding:0; box-sizing:border-box; user-select: none; }
body { background: #0f172a; overflow: hidden; font-family: 'Segoe UI', system-ui, sans-serif; }
canvas { display: block; background: #1e293b; cursor: crosshair; position: absolute; top:0; left:0; }
#ui-layer { position: absolute; top:0; left:0; width:100%; height:100%; pointer-events: none; }
.panel { background: rgba(15, 23, 42, 0.85); border: 1px solid #334155; border-radius: 8px; padding: 10px; pointer-events: auto; backdrop-filter: blur(6px); color: #e2e8f0; font-size: 13px; }
#stats { position: absolute; top: 12px; left: 12px; }
#stats span { color: #60a5fa; font-weight: bold; }
#game-title { font-size: 16px; font-weight: 800; margin-bottom: 6px; letter-spacing: 1px; color: #facc15; text-shadow: 0 0 6px rgba(234, 179, 8, 0.4); }

/* 联机操作按钮组：内嵌在 stats 面板内，分隔线下方 */
#room-controls { display: flex; gap: 5px; flex-wrap: wrap; margin-top: 8px; padding-top: 7px; border-top: 1px solid #334155; }

/* 新增：灵动岛状态栏 */
#dynamic-island { position: absolute; top: 12px; left: 50%; transform: translateX(-50%); padding: 6px 20px; border-radius: 20px; display: none; align-items: center; gap: 15px; font-weight: bold; font-size: 14px; z-index: 38; transition: all 0.3s ease; box-shadow: 0 4px 15px rgba(0,0,0,0.4); }
#dynamic-island .room-code { color: #facc15; letter-spacing: 1px; }
#dynamic-island .player-count { color: #60a5fa; }

/* 新增：抗毒血清进度条 */
#serum-container { position: absolute; bottom: 80px; left: 50%; transform: translateX(-50%); width: 200px; text-align: center; display: none; flex-direction: column; gap: 5px; z-index: 35; }
#serum-text { color: #cbd5e1; font-size: 12px; font-weight: bold; text-shadow: 0 1px 2px #000; }
#serum-bar-bg { width: 100%; height: 8px; background: rgba(0,0,0,0.6); border-radius: 4px; border: 1px solid #334155; overflow: hidden; }
#serum-bar-fill { width: 0%; height: 100%; background: linear-gradient(90deg, #22c55e, #4ade80); transition: width 0.1s linear; }
#cheat-review-overlay { position: absolute; top: 0; left: 0; width: 100%; height: 100%; background: rgba(220, 38, 38, 0.85); backdrop-filter: blur(8px); z-index: 2000; display: none; flex-direction: column; justify-content: center; align-items: center; color: #fff; pointer-events: auto; }
#cheat-review-overlay h2 { font-size: 32px; margin-bottom: 20px; letter-spacing: 2px; text-shadow: 0 2px 10px rgba(0,0,0,0.5); }
#cheat-review-overlay p { font-size: 18px; margin-bottom: 30px; }
#cheat-actions { display: flex; gap: 20px; }
#instructions { position: absolute; top: 12px; right: 12px; background: rgba(0,0,0,0.7); color: #cbd5e1; padding: 8px 12px; border-radius: 6px; font-size: 12px; line-height: 1.4; pointer-events: none; max-width: 40%; text-align: right; }
#instructions b { color: #38bdf8; }
#inv-hint { position: absolute; bottom: 80px; left: 12px; color: #94a3b8; font-size: 12px; pointer-events: none; opacity: 0.7; line-height: 1.6; }
#inv-hint b { color: #38bdf8; }
#equip-bar { position: absolute; bottom: 12px; left: 50%; transform: translateX(-50%); display: flex; flex-direction: column; gap: 6px; min-width: 300px; z-index: 35; transition: transform 0.3s, bottom 0.3s, opacity 0.3s;}
#equip-header { display: flex; justify-content: space-between; align-items: center; cursor: pointer; padding: 0 4px; font-weight: bold; color: #e2e8f0; transition: color 0.2s; }
#equip-header:hover { color: #facc15; }
#equip-toggle { font-size: 14px; transition: transform 0.3s ease; color: #94a3b8; }
#equip-wrapper { overflow: hidden; transition: max-height 0.4s cubic-bezier(0.25, 1, 0.5, 1), opacity 0.3s ease; max-height: 500px; opacity: 1; }
#equip-wrapper.collapsed { max-height: 0 !important; opacity: 0; pointer-events: none; }
#equip-wrapper.scrollable { overflow-y: auto; }
#equip-wrapper::-webkit-scrollbar { width: 5px; }
#equip-wrapper::-webkit-scrollbar-track { background: rgba(0,0,0,0.2); border-radius: 4px; }
#equip-wrapper::-webkit-scrollbar-thumb { background: #475569; border-radius: 4px; }
#equip-wrapper::-webkit-scrollbar-thumb:hover { background: #64748b; }
#equip-grid { display: flex; gap: 6px; flex-wrap: wrap; justify-content: center; padding-top: 2px; }
.modal-base { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); z-index: 50; display: none; flex-direction: column; gap: 10px; align-items: center; }
.modal-header { width: 100%; display: flex; justify-content: space-between; align-items: center; font-weight: bold; font-size: 14px; margin-bottom: 4px; }
.close-btn { cursor: pointer; opacity: 0.6; font-size: 16px; transition: 0.2s; }
.close-btn:hover { opacity: 1; transform: scale(1.2); color: #ef4444; }
#inventory-modal { min-width: 420px; max-width: 650px; }
#dev-modal { min-width: 420px; min-height: 400px; padding: 20px; display: none; flex-direction: column; justify-content: space-between; border-color: #facc15; box-shadow: 0 0 25px rgba(234, 179, 8, 0.2); z-index: 55; }
#create-room-modal, #join-room-modal, #room-settings-modal { min-width: 350px; padding: 20px; display: none; flex-direction: column; z-index: 55; }
.btn-blue { background: #2563eb !important; border-color: #60a5fa !important; color: #fff !important; }
.btn-blue:hover { background: #1d4ed8 !important; }
.btn-red { background: #dc2626 !important; border-color: #f87171 !important; color: #fff !important; }
.btn-red:hover { background: #b91c1c !important; }
.dev-input-group { display: flex; justify-content: space-between; width: 100%; align-items: center; font-size: 12px; margin-bottom: 12px; color: #cbd5e1; }
.dev-input-group input, .dev-input-group select { background: #0f172a; border: 1px solid #475569; color: #facc15; padding: 4px 8px; border-radius: 4px; width: 80px; text-align: center; font-weight: bold; outline: none; }
.dev-input-group input:focus, .dev-input-group select:focus { border-color: #38bdf8; }
.dev-input-group.full-width input { width: 100%; text-align: left; }
.room-list-area { width: 100%; max-height: 150px; overflow-y: auto; background: rgba(0,0,0,0.3); border-radius: 4px; padding: 5px; margin-bottom: 10px; border: 1px solid #475569; }
.room-item { display: flex; justify-content: space-between; align-items: center; padding: 8px; border-bottom: 1px solid #334155; font-size: 12px; }
.room-item:last-child { border-bottom: none; }
.room-item span.code { color: #facc15; font-weight: bold; }
#inv-grid { display: flex; gap: 6px; flex-wrap: wrap; justify-content: center; min-height: 46px; padding: 6px; background: rgba(0,0,0,0.2); border-radius: 6px; width: 100%; }
#craft-panel { display: flex; gap: 8px; flex-wrap: wrap; justify-content: center; width: 100%; margin-top: 4px; }
.craft-btn { padding: 6px 10px; background: #1e293b; border: 1px solid #475569; color: #94a3b8; border-radius: 6px; cursor: pointer; font-size: 12px; transition: 0.2s; }
.craft-btn:hover:not(:disabled) { background: #334155; color: #e2e8f0; border-color: #60a5fa; }
.craft-btn:disabled { opacity: 0.4; cursor: not-allowed; }
.craft-btn span { color: #facc15; }
#drag-ghost { position: fixed; pointer-events: none; z-index: 1000; display: none; width: 38px; height: 38px; border-radius: 8px; align-items: center; justify-content: center; font-weight: 800; font-size: 15px; color: #000; box-shadow: 0 4px 12px rgba(0,0,0,0.5); text-shadow: 0 0 3px #fff; }

@keyframes rainbow-bg { 0% { background-position: 0% 50%; } 50% { background-position: 100% 50%; } 100% { background-position: 0% 50%; } }
.mythic-bg { background: linear-gradient(124deg, #ff2400, #eab308, #22c55e, #0ea5e9, #a855f7, #ef4444) !important; background-size: 300% 300% !important; animation: rainbow-bg 2s ease infinite !important; color: #fff !important; text-shadow: 0 0 4px #000 !important; border-color: rgba(255,255,255,0.8) !important; }
.mythic-text { background: linear-gradient(124deg, #ff2400, #eab308, #22c55e, #0ea5e9, #a855f7, #ef4444) !important; background-size: 300% 300% !important; animation: rainbow-bg 2s ease infinite !important; -webkit-background-clip: text !important; -webkit-text-fill-color: transparent !important; font-weight: 900 !important; text-shadow: none !important; }

.overlay { backdrop-filter: blur(8px) saturate(120%); z-index: 40; opacity: 0; pointer-events: none; background: rgba(15, 23, 42, 0.6); display: flex; flex-direction: column; justify-content: center; align-items: center; width: 100%; height: 100%; transition: opacity 0.4s ease; position: absolute; top: 0; left: 0; }
.overlay.visible { opacity: 1; pointer-events: auto; }
#pause-content { display: flex; flex-direction: column; align-items: center; opacity: 0; transition: opacity 0.4s ease 0.1s, transform 0.4s ease 0.1s; transform: translateY(20px); }
#pause-content.visible { opacity: 1; transform: translateY(0); }
#pause-content h1 { color: #60a5fa; text-shadow: 0 0 20px rgba(59, 130, 246, 0.8); letter-spacing: 8px; margin-bottom: 20px; font-size: 56px; }
#pause-content p { color: #cbd5e1; letter-spacing: 2px; font-size: 20px; font-weight: bold; }

#gameOver, #multiplayerRevive { backdrop-filter: blur(10px) saturate(100%); z-index: 100; opacity: 0; pointer-events: none; background: rgba(11, 12, 16, 0.92); display: flex; flex-direction: column; justify-content: center; align-items: center; width: 100%; height: 100%; transition: opacity 0.5s ease; position: absolute; top: 0; left: 0; }
#gameOver.visible, #multiplayerRevive.visible { opacity: 1; pointer-events: auto; }
.game-over-content { display: flex; flex-direction: column; align-items: center; opacity: 0; transform: translateY(30px); transition: opacity 0.5s ease 0.2s, transform 0.5s ease 0.2s; }
.visible .game-over-content { opacity: 1; transform: translateY(0); }
.game-over-content h1 { color: #ef4444; text-shadow: 0 0 25px rgba(239, 68, 68, 0.8); letter-spacing: 6px; margin-bottom: 25px; font-size: 60px; }
.game-over-content p { color: #cbd5e1; font-size: 20px; margin-bottom: 15px; letter-spacing: 1px; }
.highlight { color: #facc15; font-weight: 800; }
.big-btn { color: #38bdf8; border: 2px solid #38bdf8; cursor: pointer; text-transform: uppercase; letter-spacing: 2px; background: transparent; border-radius: 8px; padding: 15px 45px; font-size: 22px; transition: all 0.3s; margin-top: 30px; font-weight: bold; }
.big-btn:hover { background: #38bdf8; color: #0f172a; box-shadow: 0 0 30px rgba(56, 189, 248, 0.6); transform: scale(1.05); }
</style>
</head>
<body>
<canvas id="game"></canvas>
<!-- 反作弊红屏遮罩 -->
<div id="cheat-review-overlay">
<h2>⚠️ 异常行为警报</h2>
<p id="cheat-review-text">检测到玩家异常缩放行为，房间已暂停，等待房主审核...</p>
<div id="cheat-actions" style="display: none;">
<button class="craft-btn btn-red" onclick="decideCheat('kick')">踢出作弊者并继续</button>
<button class="craft-btn btn-blue" onclick="decideCheat('ignore')">忽略并继续游戏</button>
</div>
</div>
<div class="overlay" id="pause-overlay" onclick="handleOverlayClick()">
<div id="pause-content">
<h1>已暂停</h1>
<p>[ 点击屏幕 或 空格键 继续 ]</p>
</div>
</div>

<div id="ui-layer">
<div id="stats" class="panel">
<div id="game-title">floor.oi - 生态深渊</div>
🌼 等级: <span id="lvl">1</span> | ❤️ HP: <span id="hp">100</span>/<span id="maxHp">100</span><br>
⭐ 经验: <span id="xp">0</span>/<span id="xpMax">20</span> | 🌸 最高阶: <span id="maxTier">普通</span><br>
🧭 探索深度: <span id="depth" style="color:#a78bfa;">0</span>m
<div id="room-controls">
<button class="craft-btn btn-blue" id="btn-create-room" onclick="openModal('create-room-modal')">新建房间</button>
<button class="craft-btn" id="btn-join-room" onclick="openModal('join-room-modal')">加入房间</button>
<button class="craft-btn btn-blue" id="btn-room-settings" style="display:none;" onclick="openModal('room-settings-modal')">房间设置</button>
<button class="craft-btn btn-red" id="btn-leave-room" style="display:none;" onclick="LAN.leaveRoom()">退出房间</button>
</div>
</div>

<!-- 灵动岛 -->
<div id="dynamic-island" class="panel">
<span class="room-code">房间: <span id="di-code">------</span></span>
<span class="player-count">人数: <span id="di-count">1</span>/32</span>
</div>

<!-- 抗毒血清UI -->
<div id="serum-container">
<div id="serum-text">长按 Q 键注射抗毒血清</div>
<div id="serum-bar-bg">
<div id="serum-bar-fill"></div>
</div>
</div>

<div id="inv-hint">按 <b>F12</b> 进入开发者模式<br>按 <b>空格</b> 暂停<br>按 <b>Z</b> 打开背包<br><b>Shift + 点击</b> 快速整理物品</div>
<div id="instructions">🎮 <b>左键长按</b>：提速、扩圈、发射飞刀<br>
🖱️ <b>右键长按</b>：聚拢防身、全面增加伤害<br>
🧭 <b> ` 键</b>：显示 / 隐藏中心导航<br>
💉 <b> Q 键长按</b>：注射抗毒血清(联机)
</div>

<div id="equip-bar" class="panel">
<div id="equip-header" onclick="toggleEquipBar()"><span>🎒 已装备</span><span id="equip-toggle">▼</span></div>
<div id="equip-wrapper"><div id="equip-grid"></div></div>
</div>

<!-- 模态框: 背包 -->
<div id="inventory-modal" class="panel modal-base">
<div class="modal-header">📦 背包 (不同类型同阶花瓣可任意混合合成)<span class="close-btn" onclick="closeModal('inventory-modal')">✕</span></div>
<div id="inv-grid"></div><div id="craft-panel"></div>
</div>

<!-- 模态框: 开发者 -->
<div id="dev-modal" class="panel modal-base">
<div class="modal-header" style="color: #facc15;">🛠️ 开发者控制台<span class="close-btn" onclick="closeModal('dev-modal')">✕</span></div>
<div id="dev-scroll-area" style="width:100%; max-height:300px; overflow-y:auto; padding-right:8px; margin-bottom:5px;">
<style>#dev-scroll-area::-webkit-scrollbar{width:6px}#dev-scroll-area::-webkit-scrollbar-track{background:rgba(0,0,0,0.2);border-radius:4px}#dev-scroll-area::-webkit-scrollbar-thumb{background:#475569;border-radius:4px}#dev-scroll-area::-webkit-scrollbar-thumb:hover{background:#64748b}</style>
<div class="dev-input-group"><label>掉落倍率 (推荐 0.5 ~ 5.0):</label><input type="number" id="dev-drop" step="0.1" min="0"></div>
<div class="dev-input-group"><label>合成成功率倍率:</label><input type="number" id="dev-craft" step="0.1" min="0"></div>
<div class="dev-input-group"><label>最大装配花瓣数上限:</label><input type="number" id="dev-max-equip" step="1" min="1" max="100"></div>
<div class="dev-input-group"><label>移速倍率:</label><input type="number" id="dev-speed" step="0.1" min="0.1"></div>
<div class="dev-input-group"><label>伤害倍率:</label><input type="number" id="dev-dmg" step="0.1" min="0.1"></div>
<div class="dev-input-group"><label>当前血量:</label><input type="number" id="dev-hp" step="1"></div>
<div class="dev-input-group"><label>血量回复速度 (每秒):</label><input type="number" id="dev-regen" step="1"></div>
<div class="dev-input-group"><label>经验倍率:</label><input type="number" id="dev-xp" step="0.1" min="0"></div>
</div>
<button class="craft-btn" style="width:100%; border-color:#a855f7; color:#c084fc; font-weight:bold; margin-bottom:5px; font-size: 13px; letter-spacing: 1px;" onclick="clearAllEnemies()">🛡️ 清除所有敌人</button>
<div style="display:flex; gap:10px; width:100%;">
<button class="craft-btn btn-red" style="flex:1;" onclick="resetDevSettings()">恢复初始设置</button>
<button class="craft-btn btn-blue" style="flex:1;" onclick="applyDevSettings()">应用并保存</button>
</div>
</div>

<!-- 模态框: 创建房间 -->
<div id="create-room-modal" class="panel modal-base">
<div class="modal-header" style="color: #60a5fa;">🏠 创建联机房间<span class="close-btn" onclick="closeModal('create-room-modal')">✕</span></div>
<div style="width:100%; margin-top: 10px;">
<div class="dev-input-group">
<label>服务器IP后4位 (局域网):</label>
<div style="display:flex; align-items:center; gap:5px;">
<span style="color:#94a3b8">192.168.</span>
<input type="text" id="ipt-server-ip" placeholder="x.x" value="1.2" style="width:60px;">
</div>
</div>
<div class="dev-input-group">
<label>玩家昵称:</label>
<input type="text" id="ipt-cr-name" maxlength="16" placeholder="输入昵称">
</div>
<div class="dev-input-group">
<label>作弊处理规则:</label>
<select id="sel-cr-cheat">
<option value="0">交由房主审核 (推荐)</option>
<option value="1">允许作弊继续</option>
</select>
</div>
<div class="dev-input-group" style="justify-content: flex-start; gap: 10px;">
<input type="checkbox" id="chk-cr-dev" checked style="width: auto;">
<label for="chk-cr-dev">允许非房主玩家使用开发者模式</label>
</div>
<p style="font-size: 11px; color:#94a3b8; margin: 10px 0;">* 创建后将自动生成6位数字房间码，局域网内玩家可通过房间码加入。</p>
<div style="display:flex; gap:10px; width:100%;">
<button class="craft-btn" style="flex:1;" onclick="closeModal('create-room-modal')">取消</button>
<button class="craft-btn btn-blue" style="flex:1;" onclick="LAN.createRoom()">创建并进入</button>
</div>
</div>
</div>

<!-- 模态框: 加入房间 -->
<div id="join-room-modal" class="panel modal-base">
<div class="modal-header" style="color: #60a5fa;">🔌 加入联机房间<span class="close-btn" onclick="closeModal('join-room-modal')">✕</span></div>
<div style="width:100%; margin-top: 10px;">
<div class="dev-input-group">
<label>服务器IP后4位:</label>
<div style="display:flex; align-items:center; gap:5px;">
<span style="color:#94a3b8">192.168.</span>
<input type="text" id="ipt-join-server-ip" placeholder="x.x" value="1.2" style="width:60px;">
</div>
</div>
<div class="dev-input-group">
<label>玩家昵称:</label>
<input type="text" id="ipt-join-name" maxlength="16" placeholder="输入昵称">
</div>
<div class="dev-input-group">
<label>6位房间码:</label>
<input type="text" id="ipt-join-code" maxlength="6" placeholder="123456" oninput="this.value=this.value.replace(/[^0-9]/g,'')">
</div>
<div style="font-size: 12px; color:#cbd5e1; margin-bottom: 4px;">局域网已发现房间: (自动刷新)</div>
<div class="room-list-area" id="lan-room-list">
<div style="text-align:center; color:#64748b; padding:10px;">暂无可用房间</div>
</div>
<div style="display:flex; gap:10px; width:100%; margin-top:10px;">
<button class="craft-btn" style="flex:1;" onclick="closeModal('join-room-modal')">取消</button>
<button class="craft-btn btn-blue" style="flex:1;" onclick="LAN.joinRoom()">加入房间</button>
</div>
</div>
</div>

<!-- 模态框: 房间设置 -->
<div id="room-settings-modal" class="panel modal-base">
<div class="modal-header" style="color: #facc15;">⚙️ 房间设置 (仅房主)<span class="close-btn" onclick="closeModal('room-settings-modal')">✕</span></div>
<div style="width:100%; margin-top: 10px;">
<div class="dev-input-group">
<label>作弊处理规则:</label>
<select id="sel-rs-cheat">
<option value="0">交由房主审核</option>
<option value="1">允许作弊继续</option>
</select>
</div>
<div class="dev-input-group" style="justify-content: flex-start; gap: 10px;">
<input type="checkbox" id="chk-rs-dev" style="width: auto;">
<label for="chk-rs-dev">允许非房主玩家使用开发者模式</label>
</div>
<div style="display:flex; gap:10px; width:100%; margin-top:15px;">
<button class="craft-btn" style="flex:1;" onclick="closeModal('room-settings-modal')">取消</button>
<button class="craft-btn btn-blue" style="flex:1;" onclick="LAN.updateSettings()">保存修改</button>
</div>
</div>
</div>
</div>

<div id="drag-ghost"></div>

<!-- 单人死亡 -->
<div id="gameOver">
<div class="game-over-content">
<h1>💀 花朵凋零</h1>
<p>探索在第 <span id="finalPos" class="highlight">0</span> 米的深渊被终结</p>
<p style="font-size: 18px; color: #38bdf8; margin-bottom: 20px;">最终等级 <span id="finalLvl" style="font-weight:700; color:#fff;">1</span> | 存活 <span id="survTime" style="font-weight:700; color:#fff;">0</span>s</p>
<button class="big-btn" onclick="location.reload()">重新探索</button>
</div>
</div>

<!-- 联机死亡 -->
<div id="multiplayerRevive">
<div class="game-over-content">
<h1>💀 你已被击败</h1>
<p>可复活在地图中心，等级、背包将被<span class="highlight">重置</span></p>
<p style="font-size: 16px; color: #94a3b8; margin-bottom: 20px;">(开发者模式倍率配置保留)</p>
<button class="big-btn" onclick="LAN.revive()">立即复活</button>
</div>
</div>

<script>
// ==================== 核心单人游戏引擎与变量 ====================
const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
function resize() { canvas.width = window.innerWidth; canvas.height = window.innerHeight; }
window.addEventListener('resize', resize); resize();

let running = true, frame = 0, startTime = Date.now();
let mouseX = canvas.width/2, mouseY = canvas.height/2;
window.addEventListener('mousemove', e => { mouseX = e.clientX; mouseY = e.clientY; });

let shiftPressed = false, isPaused = false, isPausedBySpace = false, activeModal = null;
let equipCollapsed = false, showNav = false, navProgress = 0, needsUIRender = true, screenShake = 0;      
let isLeftMouseDown = false, isRightMouseDown = false, currentRadiusOffset = 0, equipZIndexTimer = null;

let sessionDropMult = 0.85 + Math.random() * 0.3, sessionCraftMult = 0.95 + Math.random() * 0.1, maxEquipLimit = 12;
let sessionSpeedMult = 1.0, sessionDmgMult = 1.0, sessionXpMult = 1.0, sessionHpRegen = 1;

window.addEventListener('mousedown', e => { if (e.button===0) isLeftMouseDown=true; if (e.button===2) isRightMouseDown=true; });
window.addEventListener('mouseup', e => { if (e.button===0) isLeftMouseDown=false; if (e.button===2) isRightMouseDown=false; });
window.addEventListener('contextmenu', e => e.preventDefault());

const camera = { x: 0, y: 0 };
const player = { x: 0, y: 0, vx: 0, vy: 0, r: 18, baseSpeed: 3.5, hp: 100, maxHp: 100, lvl: 1, xp: 0, xpMax: 20, invincibleTimer: 0, hurtFlashTimer: 0 };

const TIERS =['common', 'unusual', 'rare', 'epic', 'legendary', 'mythic'];
const TIER_NAMES = { common:'普通', unusual:'不凡', rare:'稀有', epic:'史诗', legendary:'传奇', mythic:'至臻' };
const TIER_COLORS = { common:'#22c55e', unusual:'#eab308', rare:'#3b82f6', epic:'#a855f7', legendary:'#ef4444', mythic:'#ffffff' };
const TIER_STATS = { 
common:   { dmg:3, speedBonus:1.0, regen: 180, ffDmg: 5 }, unusual:  { dmg:5, speedBonus:1.1, regen: 150, ffDmg: 8 }, 
rare:     { dmg:8, speedBonus:1.2, regen: 120, ffDmg: 12 }, epic:     { dmg:12, speedBonus:1.35, regen: 90, ffDmg: 15 }, 
legendary:{ dmg:18, speedBonus:1.5, regen: 60, ffDmg: 20 }, mythic:   { dmg:28, speedBonus:1.8, regen: 30, ffDmg: 28 }
};
const KIND_ICONS = { melee:'', explosive:'爆', missile:'射', speed:'速', guardian:'护' };
const BASE_CRAFT_RATES = { common:0.68, unusual:0.58, rare:0.48, epic:0.38, legendary:0.28 };
const CRAFT_RATES = {};
for (let key in BASE_CRAFT_RATES) CRAFT_RATES[key] = Math.min(1.0, BASE_CRAFT_RATES[key] * sessionCraftMult);

function getPetalColor(type, offset=0) { return type==='mythic' ? `hsl(${((frame+offset)*3)%360}, 100%, 60%)` : TIER_COLORS[type]; }

let equipped =[], inventory = [], groundPetals = [], enemies =[], particles =[], damageTexts =[], blastWaves =[];
const rand = (a,b)=>Math.random()*(b-a)+a; const dist = (a,b)=>Math.hypot(a.x-b.x, a.y-b.y); const collides = (a,b)=>dist(a,b)<a.r+b.r;
const randomKind = () => { const r=Math.random(); if(r<0.35) return 'melee'; if(r<0.55) return 'missile'; if(r<0.75) return 'explosive'; if(r<0.95) return 'speed'; return 'guardian'; };
const createPetal = (type, x=0, y=0, kind=randomKind())=>({ id: Date.now()+Math.random(), type: String(type), kind: kind, dmg: TIER_STATS[String(type)].dmg, r: 8, x, y, durability: 5, maxDurability: 5, isActive: true, cooldownTimer: 0, missileState: 'idle', missileTimer: 0, missileVx: 0, missileVy: 0 });

// 初始装备
function resetInventory() {
equipped = []; inventory = []; groundPetals =[];
for(let i=0;i<4;i++) equipped.push(createPetal('common', 0, 0, 'melee'));
needsUIRender = true;
}
resetInventory();

function getCurrentMaxTierIdx() { const all =[...equipped, ...inventory]; return all.length ? Math.max(...all.map(p => TIERS.indexOf(p.type))) : 0; }
function spawnParticles(x, y, color, count=5, speedMult=1){ for(let i=0;i<count;i++) particles.push({x, y, vx:rand(-3,3)*speedMult, vy:rand(-3,3)*speedMult, life:rand(15,40), color, r:rand(2,5)}); }
function spawnDamageText(x, y, text, color) { damageTexts.push({ x: x+rand(-10,10), y: y-10, text, color, life: 40, maxLife: 40, vy: -1.5 }); }
function getMapDepth() { return Math.floor(Math.hypot(player.x, player.y) / 100); }

function dropGroundPetal(x, y) {
const depth = getMapDepth(); let maxIdx = getCurrentMaxTierIdx();
if (depth > 20 && maxIdx < TIERS.length - 1 && Math.random() < 0.05 * sessionDropMult) maxIdx++;
let dropIdx = 0;
for (let i = maxIdx; i >= 0; i--) { 
let dropChance = Math.min((0.35 / Math.pow(2.2, i) + (depth * 0.0015)) * sessionDropMult, 0.85); 
if (i === 0 || Math.random() < dropChance) { dropIdx = i; break; } 
}
const p = createPetal(TIERS[dropIdx], x, y); p.life = 600; groundPetals.push(p); needsUIRender = true;
}

// ==================== 模态框与UI管理系统 (兼容联机) ====================
function openModal(id) {
if (activeModal && activeModal !== id) closeModal(activeModal);
// 打开背包/开发者面板时清除空格暂停标志，关闭后不残留暂停（与单机版一致）
if (id === 'inventory-modal' || id === 'dev-modal') isPausedBySpace = false;
activeModal = id;
document.getElementById(id).style.display = 'flex';
if(id === 'dev-modal') {
document.getElementById('dev-drop').value = sessionDropMult.toFixed(2);
document.getElementById('dev-craft').value = sessionCraftMult.toFixed(2);
document.getElementById('dev-max-equip').value = maxEquipLimit;
document.getElementById('dev-speed').value = sessionSpeedMult.toFixed(2);
document.getElementById('dev-dmg').value = sessionDmgMult.toFixed(2);
document.getElementById('dev-hp').value = Math.ceil(player.hp);
document.getElementById('dev-regen').value = sessionHpRegen;
document.getElementById('dev-xp').value = sessionXpMult.toFixed(2);
} else if(id === 'create-room-modal' || id === 'join-room-modal') {
const randName = "User_" + Math.floor(Math.random()*9000 + 1000);
document.getElementById('ipt-cr-name').value = randName;
document.getElementById('ipt-join-name').value = randName;
} else if(id === 'room-settings-modal') {
document.getElementById('sel-rs-cheat').value = LAN.roomConfig.cheatRule;
document.getElementById('chk-rs-dev').checked = LAN.roomConfig.devModeAllowed;
}
needsUIRender = true; updatePauseState();
}
function closeModal(id) { document.getElementById(id).style.display = 'none'; activeModal = null; updatePauseState(); }
function toggleEquipBar() {
equipCollapsed = !equipCollapsed;
const wrapper = document.getElementById('equip-wrapper'), toggleBtn = document.getElementById('equip-toggle');
if (equipCollapsed) { wrapper.classList.add('collapsed'); toggleBtn.textContent = '▲'; } 
else { wrapper.classList.remove('collapsed'); toggleBtn.textContent = '▼'; needsUIRender = true; }
}
function togglePause() { if (!activeModal) { isPausedBySpace = !isPausedBySpace; updatePauseState(); } }
function updatePauseState() {
isPaused = activeModal !== null || isPausedBySpace || LAN.isRoomPaused;
const overlay = document.getElementById('pause-overlay'), content = document.getElementById('pause-content'), equipBar = document.getElementById('equip-bar');
if (equipZIndexTimer) { clearTimeout(equipZIndexTimer); equipZIndexTimer = null; }
if (isPaused && !LAN.isRoomPaused) { // 红屏时不由这里控
overlay.classList.add('visible');
equipBar.style.zIndex = activeModal==='inventory-modal' ? '45' : '35';
if (isPausedBySpace && !activeModal) content.classList.add('visible'); else content.classList.remove('visible');
} else {
overlay.classList.remove('visible'); content.classList.remove('visible');
equipZIndexTimer = setTimeout(() => { if (!isPaused) equipBar.style.zIndex = '35'; equipZIndexTimer = null; }, 400); 
}
}
function handleOverlayClick() { if (activeModal) closeModal(activeModal); else if (isPausedBySpace) togglePause(); }

// 键盘事件 (结合联机与单机)
window.addEventListener('keydown', e => { 
if (!running) return;
if(e.key === 'Shift') shiftPressed = true; 
if(e.key.toLowerCase() === 'z' && !e.repeat) {
if(activeModal==='inventory-modal') closeModal('inventory-modal'); else openModal('inventory-modal');
}
if(e.key === 'F12' && !e.repeat) {
e.preventDefault();
if(LAN.isMultiplayer && !LAN.isHost && !LAN.roomConfig.devModeAllowed) {
spawnDamageText(player.x, player.y, "房主已禁用非房主开发者模式", "#ef4444"); return;
}
if(activeModal==='dev-modal') closeModal('dev-modal'); else openModal('dev-modal');
}
if ((e.key === ' ' || e.code === 'Space') && !e.repeat) {
if (activeModal) {
// 任何模态框（含创建/加入房间）：先失焦输入框，再强制关闭
if (document.activeElement && typeof document.activeElement.blur === 'function') document.activeElement.blur();
closeModal(activeModal);
} else if (!document.activeElement.tagName.match(/INPUT|SELECT/)) {
togglePause();
}
}
if (e.key === '`' || e.code === 'Backquote') showNav = !showNav;
if (e.key.toLowerCase() === 'q' && LAN.isMultiplayer && LAN.bleedStacks > 0) LAN.startSerum();
});
window.addEventListener('keyup', e => { 
if(e.key === 'Shift') shiftPressed = false; 
if(e.key.toLowerCase() === 'q') LAN.stopSerum();
});

// ==================== 联机核心系统 (NetworkManager & Logic) ====================
const LAN = {
isMultiplayer: false, isHost: false, roomCode: '', clientId: '',
players: {}, 
playerPetalsState: {}, // 存储玩家花瓣的动态状态
roomConfig: { cheatRule: 0, devModeAllowed: true },
isRoomPaused: false, bleedStacks: 0, isInjecting: false, serumProgress: 0, bleedStartTime: 0, lastBleedTick: 0,
serverUrl: '', 
pollTimer: null, beaconTimer: null, targetCheatId: '',

init() {
this.clientId = 'CLI_' + Math.floor(Math.random()*1000000);
setInterval(() => this.antiCheatCheck(), 2000);
// 【新增】自动扫描局域网房间
setInterval(() => this.scanRooms(), 2000); 
},
// 更新玩家花瓣的动态状态
updatePetalsState(dt) {
for (const playerId in this.playerPetalsState) {
const state = this.playerPetalsState[playerId];
const petals = state.petals;
for (let i = 0; i < petals.length; i++) {
const petalState = petals[i];
// 平滑调整速度到目标值
const speedDiff = petalState.targetSpeed - petalState.currentSpeed;
petalState.currentSpeed += speedDiff * 0.1; // 平滑系数
// 更新角度
petalState.angle += petalState.currentSpeed * dt;
// 归一化角度
petalState.angle = petalState.angle % (Math.PI * 2);
if (petalState.angle < 0) petalState.angle += Math.PI * 2;
}
}
},
// 平滑插值其他玩家的显示位置
	updatePlayerInterpolation(dt) {
		for (const playerId in this.playerPetalsState) {
			const state = this.playerPetalsState[playerId];
			if (!this.players[playerId]) continue;
			const dx = state.targetX - state.displayX;
			const dy = state.targetY - state.displayY;
			const dist = Math.hypot(dx, dy);
			if (dist < 0.5) {
				state.displayX = state.targetX;
				state.displayY = state.targetY;
			} else {
				let lerpFactor;
				if (dist < 5) {
					lerpFactor = 0.12;
				} else if (dist < 30) {
					lerpFactor = 0.18;
				} else if (dist < 100) {
					lerpFactor = 0.28;
				} else if (dist < 300) {
					lerpFactor = 0.40;
				} else {
					lerpFactor = 0.55;
				}
				lerpFactor = 1 - Math.pow(1 - lerpFactor, dt);
				state.displayX += dx * lerpFactor;
				state.displayY += dy * lerpFactor;
			}
			state.x = state.displayX;
			state.y = state.displayY;
			this.players[playerId].x = state.displayX;
			this.players[playerId].y = state.displayY;
		}
	},

	// 计算花瓣的世界坐标
	getPetalWorldPosition(playerId, petalIndex) {
const state = this.playerPetalsState[playerId];
if (!state || !state.petals[petalIndex]) return null;
const petalState = state.petals[petalIndex];
return {
x: state.x + Math.cos(petalState.angle) * petalState.radius,
y: state.y + Math.sin(petalState.angle) * petalState.radius,
angle: petalState.angle
};
},

getBaseUrl(prefix) {
let ipPart = prefix.trim() || "1.2";
return `http://192.168.${ipPart}:28082`;
},

async _fetch(endpoint, bodyObj, prefixIp) {
try {
const url = this.getBaseUrl(prefixIp) + endpoint;
const res = await fetch(url, { method: 'POST', headers:{'Content-Type':'application/json'}, body: JSON.stringify(bodyObj) });
return await res.json();
} catch(e) { console.error(e); return {success: false, message: "网络请求失败，请确保服务端已运行"}; }
},

// 【新增】扫描房间替代UDP广播（解决浏览器无法接收UDP的限制）
async scanRooms() {
if (this.isMultiplayer || document.getElementById('join-room-modal').style.display !== 'flex') return;
const ip = document.getElementById('ipt-join-server-ip').value || '1.2';
try {
const res = await fetch(`http://192.168.${ip}:28082/api/room/list`);
const data = await res.json();
const listArea = document.getElementById('lan-room-list');
if (data.rooms.length === 0) {
listArea.innerHTML = '<div style="text-align:center; color:#64748b; padding:10px;">暂无可用房间</div>';
return;
}
listArea.innerHTML = '';
data.rooms.forEach(r => {
listArea.innerHTML += `
<div class="room-item">
    <span><span class="code">${r.code}</span> - ${r.name} (${r.count}/32)</span>
    <button class="craft-btn btn-blue" onclick="document.getElementById('ipt-join-code').value='${r.code}'">选择</button>
</div>`;
});
} catch(e) {}
},

async createRoom() {
const ip = document.getElementById('ipt-server-ip').value;
const name = document.getElementById('ipt-cr-name').value.trim() || 'Player';
const rule = document.getElementById('sel-cr-cheat').value;
const devAllowed = document.getElementById('chk-cr-dev').checked;

const res = await this._fetch('/api/room/create', { client_id: this.clientId, name, cheat_rule: rule, dev_mode_allowed: devAllowed }, ip);
if(res.success) {
this.serverUrl = this.getBaseUrl(ip);
this.roomCode = res.code; this.isHost = true; this.isMultiplayer = true;
this.roomConfig = { cheatRule: parseInt(rule), devModeAllowed: devAllowed };
this.enterMultiplayerMode();
closeModal('create-room-modal');
} else alert(res.message);
},

async joinRoom() {
const ip = document.getElementById('ipt-join-server-ip').value;
const name = document.getElementById('ipt-join-name').value.trim() || 'Player';
const code = document.getElementById('ipt-join-code').value.trim();
if(code.length !== 6) return alert("请输入6位房间码");

const res = await this._fetch('/api/room/join', { client_id: this.clientId, name, code }, ip);
if(res.success) {
this.serverUrl = this.getBaseUrl(ip);
this.roomCode = code; this.isHost = false; this.isMultiplayer = true;
this.enterMultiplayerMode();
closeModal('join-room-modal');
} else alert(res.message);
},

leaveRoom() {
// 注：由于服务端无显式leave端点，利用5秒心跳超时自动清理退出玩家
this.playerPetalsState = {}; // 清理花瓣状态
this.resetToSingle();
},

resetToSingle() {
this.isMultiplayer = false; this.isHost = false; this.roomCode = ''; this.players = {};
this.clearBleed(); this.stopSerum(); this.isRoomPaused = false;
clearInterval(this.pollTimer);
document.getElementById('cheat-review-overlay').style.display = 'none';
document.getElementById('dynamic-island').style.display = 'none';
document.getElementById('btn-create-room').style.display = 'inline-block';
document.getElementById('btn-join-room').style.display = 'inline-block';
document.getElementById('btn-room-settings').style.display = 'none';
document.getElementById('btn-leave-room').style.display = 'none';
resetInventory(); player.hp = player.maxHp; player.lvl = 1; player.xp = 0; enemies =[]; needsUIRender = true;
},

enterMultiplayerMode() {
resetInventory();
document.getElementById('btn-create-room').style.display = 'none';
document.getElementById('btn-join-room').style.display = 'none';
document.getElementById('btn-leave-room').style.display = 'inline-block';
if(this.isHost) document.getElementById('btn-room-settings').style.display = 'inline-block';

document.getElementById('dynamic-island').style.display = 'flex';
document.getElementById('di-code').innerText = this.roomCode;

// 【修改】真实接入状态同步：每100ms与服务端握手同步坐标
this.pollTimer = setInterval(() => this.syncState(), 100);
player.x = rand(-100, 100); player.y = rand(-100, 100); 
},

// 【新增】真实的状态同步函数
async syncState() {
if(this.isRoomPaused) return; 
try {
// 发送花瓣数据（安全检查）
const petalsData = (equipped || []).map(p => ({
type: String(p.type || 'common'),
kind: String(p.kind || 'melee'),
x: Number(p.x || 0),
y: Number(p.y || 0),
isActive: Boolean(p.isActive)
}));
const state = {
client_id: this.clientId, room_code: this.roomCode,
x: player.x, y: player.y, 
hp: player.hp, isBleeding: this.bleedStacks > 0,
petals: petalsData
};
// 调试日志
if (window._debug) {
console.log('发送状态:', state);
}
const res = await fetch(this.serverUrl + '/api/state', { 
method: 'POST', 
body: JSON.stringify(state),
signal: AbortController ? new AbortController().signal : undefined
});
const data = await res.json();
// 调试日志
if (window._debug) {
console.log('接收数据:', data);
}
if(data.success) {
this.players = {};
const currentTime = Date.now();
for (const id in data.players) {
const p = data.players[id];
if (id === this.clientId) {
const serverHp = Number(p.hp || player.hp);
if (serverHp < player.hp) {
player.hp = serverHp; player.hurtFlashTimer = 5;
}
if (Boolean(p.isBleeding) && this.bleedStacks === 0) {
this.addBleed(1);
}
needsUIRender = true;
continue;
}
this.players[id] = {
x: Number(p.x || 0),
y: Number(p.y || 0),
hp: Number(p.hp || 100),
isBleeding: Boolean(p.isBleeding),
name: String(p.name || 'Unknown'),
petals: (p.petals || []).map(petal => ({
type: String(petal.type || 'common'),
kind: String(petal.kind || 'melee'),
isActive: Boolean(petal.isActive)
})).slice(0, 100)
};
// 初始化或更新花瓣动态状态
			if (!this.playerPetalsState[id]) {
				this.playerPetalsState[id] = {
					petals: [],
					lastUpdateTime: currentTime,
					baseRotationSpeed: 0.035, // 默认旋转速度
					displayX: Number(p.x || 0),
					displayY: Number(p.y || 0),
					targetX: Number(p.x || 0),
					targetY: Number(p.y || 0)
				};
			}
			const state = this.playerPetalsState[id];
			const prevTargetX = state.targetX;
			const prevTargetY = state.targetY;
			state.targetX = this.players[id].x;
			state.targetY = this.players[id].y;
			if (state.displayX === undefined) {
				state.displayX = state.targetX;
				state.displayY = state.targetY;
			}
			const timeDelta = (currentTime - state.lastUpdateTime) / 1000;
			if (timeDelta > 0 && timeDelta < 1) {
				state.velocityX = (state.targetX - prevTargetX) / timeDelta;
				state.velocityY = (state.targetY - prevTargetY) / timeDelta;
			}
			state.x = state.targetX;
			state.y = state.targetY;
			state.lastUpdateTime = currentTime;
// 同步花瓣数量
const targetPetalsCount = this.players[id].petals.length;
if (state.petals.length !== targetPetalsCount) {
// 重建花瓣状态数组
state.petals = [];
for (let i = 0; i < targetPetalsCount; i++) {
const baseAngle = (Math.PI * 2 / Math.max(1, targetPetalsCount)) * i;
state.petals.push({
angle: baseAngle,
currentSpeed: state.baseRotationSpeed,
targetSpeed: state.baseRotationSpeed,
radius: 45 + (targetPetalsCount * 1.5) // 基础半径
});
}
}
}
document.getElementById('di-count').innerText = Object.keys(this.players).length + 1;
} else {
alert("房间已断开连接！");
this.leaveRoom();
}
} catch(e) {
console.warn("Sync state error:", e);
}
},

startSerum() { if(!this.isInjecting && this.bleedStacks > 0) { this.isInjecting = true; this.serumProgress = 0; document.getElementById('serum-container').style.display='flex'; } },
stopSerum() { this.isInjecting = false; this.serumProgress = 0; document.getElementById('serum-container').style.display='none'; document.getElementById('serum-bar-fill').style.width = '0%'; document.getElementById('serum-text').innerText = "长按 Q 键注射抗毒血清"; },
updateSerum() {
if(!this.isInjecting) return;
this.serumProgress += 1000/60; 
let pct = Math.min((this.serumProgress / 3000) * 100, 100);
document.getElementById('serum-bar-fill').style.width = pct + '%';
document.getElementById('serum-text').innerText = `注射中 ${Math.floor(pct)}% · 移速降低20%`;
spawnParticles(player.x, player.y, '#22c55e', 1, 0.5); 
if(this.serumProgress >= 3000) {
this.clearBleed(); this.stopSerum();
spawnDamageText(player.x, player.y - 20, "流血已解除", "#22c55e");
}
},
addBleed(stacks) {
if (!this.isMultiplayer) return;
if (this.bleedStacks === 0) this.bleedStartTime = Date.now();
this.bleedStacks += stacks;
},
clearBleed() {
this.bleedStacks = 0; this.bleedStartTime = 0; this.lastBleedTick = 0;
},
applyBleed() {
if(this.bleedStacks <= 0) return;
var now = Date.now();
var elapsed = (now - this.bleedStartTime) / 1000;
var dmgPerSec = 1 + Math.floor(elapsed / 5);
var interval = Math.max(1, Math.floor(60 / dmgPerSec));
if (frame - this.lastBleedTick >= interval) {
this.lastBleedTick = frame;
player.hp -= 1; player.hurtFlashTimer = 5;
spawnDamageText(player.x, player.y, "-1 \u6d41\u8840", "#dc2626");
spawnParticles(player.x, player.y, '#991b1b', 1, 0.3);
if(player.hp <= 0) gameOver(true);
needsUIRender = true;
}
},
sendDamageToServer(targetClientId, damage, isBleedAttack, duration) {
if(!this.isMultiplayer || !this.serverUrl) return;
var payload = {
client_id: this.clientId,
room_code: this.roomCode,
target_id: targetClientId,
damage: damage,
is_bleed_attack: !!isBleedAttack,
duration: duration || 0
};
fetch(this.serverUrl + '/api/damage', {
method: 'POST',
headers: {'Content-Type':'application/json'},
body: JSON.stringify(payload)
}).catch(function(){});
},

initialRatio: window.devicePixelRatio,
antiCheatCheck() {
if(!this.isMultiplayer || this.isRoomPaused) return;
let diffRatio = Math.abs(window.devicePixelRatio - this.initialRatio);
if (diffRatio > 0.1) {
// 目前作弊仅在本地输出拦截，可在此请求 /api/cheat/report
console.warn("作弊检测: 发现异常页面缩放");
this.initialRatio = window.devicePixelRatio; 
}
},

revive() {
document.getElementById('multiplayerRevive').classList.remove('visible');
player.x = 0; player.y = 0; player.hp = player.maxHp = 100; player.lvl = 1; player.xp = 0;
this.clearBleed(); this.stopSerum(); resetInventory();
for(let id in this.players) { if(dist(player, this.players[id]) < 40) player.x += rand(50, 100); }
isPaused = false; running = true; loop();
}
};
LAN.init();

function decideCheat(action) { // 房主点击
fetch(LAN.serverUrl + '/api/cheat/decide', { method:'POST', body:JSON.stringify({owner_id:LAN.clientId, code:LAN.roomCode, target_id:LAN.targetCheatId, action})}).catch(()=>{});
document.getElementById('cheat-review-overlay').style.display = 'none'; LAN.isRoomPaused = false;
}

// ==================== UI 渲染与通用逻辑 ====================
function renderUINow() {
document.getElementById('lvl').textContent = player.lvl;
document.getElementById('hp').textContent = Math.ceil(player.hp);
document.getElementById('maxHp').textContent = player.maxHp;
document.getElementById('xp').textContent = player.xp;
document.getElementById('xpMax').textContent = player.xpMax;
const maxTierSpan = document.getElementById('maxTier');
maxTierSpan.textContent = TIER_NAMES[TIERS[getCurrentMaxTierIdx()]];
if(TIERS[getCurrentMaxTierIdx()] === 'mythic') maxTierSpan.classList.add('mythic-text'); else maxTierSpan.classList.remove('mythic-text');
document.getElementById('depth').textContent = getMapDepth();

const eqGrid = document.getElementById('equip-grid'); eqGrid.innerHTML = ''; 
const eqFrag = document.createDocumentFragment();
equipped.forEach(p => {
const s = document.createElement('div'); const col = TIER_COLORS[p.type];
s.style.cssText = `background:${col}; border:2px solid rgba(255,255,255,0.3); border-radius:8px; width:38px; height:38px; display:flex; align-items:center; justify-content:center; font-weight:800; font-size:15px; color:#000; position:relative; opacity:${p.isActive?1:0.3}; box-shadow:0 2px 6px rgba(0,0,0,0.3); cursor:pointer;`;
if(p.type === 'mythic') s.classList.add('mythic-bg');
let iconHtml = KIND_ICONS[p.kind] ? `<div style="position:absolute; top:2px; right:3px; color:#fff; font-size:10px; font-weight:bold; text-shadow:0 0 2px #000; pointer-events:none;">${KIND_ICONS[p.kind]}</div>` : '';
s.innerHTML = `${p.dmg}${iconHtml}<div style="position:absolute; bottom:0; left:0; width:100%; height:3px; background:${p.durability>2?'#22c55e':'#ef4444'}; transition:height 0.15s; border-radius: 0 0 6px 6px;"></div>`;
s.title = `品质: ${TIER_NAMES[p.type]}\n耐久: ${p.durability}/${p.maxDurability}`;
s.onmousedown = (e) => { if(shiftPressed) quickSwap(p, 'equip'); else startDrag(e, p, 'equip'); };
eqFrag.appendChild(s);
}); eqGrid.appendChild(eqFrag);
// 装备栏超过68个时锁定高度并启用滚动
const eqWrapper = document.getElementById('equip-wrapper');
if (!equipCollapsed) {
  if (equipped.length > 68) {
    const items = eqGrid.children;
    if (items.length >= 68) {
      const item = items[67];
      const gridTop = eqGrid.getBoundingClientRect().top;
      const itemBottom = item.getBoundingClientRect().bottom;
      const lockedH = Math.round(itemBottom - gridTop) + 4;
      eqWrapper.style.maxHeight = lockedH + 'px';
      eqWrapper.classList.add('scrollable');
    }
  } else {
    eqWrapper.style.maxHeight = '500px';
    eqWrapper.classList.remove('scrollable');
  }
}

if(activeModal === 'inventory-modal') {
const invGrid = document.getElementById('inv-grid'); invGrid.innerHTML = '';
const counts = {}; inventory.forEach(p => counts[p.type+'|'+p.kind] = (counts[p.type+'|'+p.kind]||0)+1);
const KIND_ORDER = { melee:0, explosive:1, missile:2, speed:3, guardian:4 };
Object.keys(counts).sort((a,b)=>{ const [ta,ka]=a.split('|'), [tb,kb]=b.split('|'); const td=TIERS.indexOf(ta)-TIERS.indexOf(tb); return td!==0?td:(KIND_ORDER[ka]||0)-(KIND_ORDER[kb]||0); }).forEach(key => {
const [tier, kind] = key.split('|'); const count = counts[key];
const el = document.createElement('div'); const col = TIER_COLORS[tier];
el.style.cssText = `background:${col}; border:2px solid rgba(255,255,255,0.3); border-radius:8px; width:42px; height:42px; display:flex; align-items:center; justify-content:center; font-weight:800; font-size:15px; color:#000; position:relative; cursor:grab;`;
if(tier === 'mythic') el.classList.add('mythic-bg');
let iconHtml = KIND_ICONS[kind] ? `<div style="position:absolute; top:2px; right:3px; color:#fff; font-size:10px; font-weight:bold; text-shadow:0 0 2px #000; pointer-events:none;">${KIND_ICONS[kind]}</div>` : '';
el.innerHTML = `<span>${TIER_STATS[tier].dmg}</span>${iconHtml}<span style="position:absolute;bottom:2px;left:3px;background:rgba(0,0,0,0.65);color:#fff;border-radius:4px;padding:0 4px;font-size:11px;font-weight:600;pointer-events:none;">x${count}</span>`;
el.onmousedown = (e) => { if(shiftPressed) quickSwapByKey(key, 'inv'); else startDragByKey(e, key); };
invGrid.appendChild(el);
});
const panel = document.getElementById('craft-panel'); panel.innerHTML = '';
TIERS.slice(0, -1).forEach(t => {
const count = inventory.filter(p=>p.type===t).length;
const btn = document.createElement('button'); btn.className = 'craft-btn'; btn.disabled = count < 5;
btn.innerHTML = `5 ${TIER_NAMES[t]} → ${TIER_NAMES[TIERS[TIERS.indexOf(t)+1]]} (<span>${Math.floor(CRAFT_RATES[t]*100)}%</span>)`;
btn.onclick = () => craft(t); panel.appendChild(btn);
});
}
}

// 拖拽与装备
function quickSwap(p, src) { const sArr=src==='equip'?equipped:inventory, dArr=src==='equip'?inventory:equipped; const i=sArr.indexOf(p); if(i>-1 && (src==='equip' || dArr.length<maxEquipLimit)) { sArr.splice(i,1); dArr.push(p); needsUIRender=true; } }
function quickSwapByKey(k, src) { if(src==='inv' && equipped.length<maxEquipLimit) { const [t, kd]=k.split('|'), i=inventory.findIndex(p=>p.type===t&&p.kind===kd); if(i>-1) { equipped.push(inventory.splice(i,1)[0]); needsUIRender=true; } } }
let dragData = null, ghost = document.getElementById('drag-ghost');
function startDragByKey(e, k) { e.preventDefault(); const [t, kd]=k.split('|'), i=inventory.findIndex(p=>p.type===t&&p.kind===kd); if(i===-1) return; dragData = { petal: inventory.splice(i,1)[0], source: 'inv', originalIndex: i }; setupDragGhost(e, dragData.petal); }
function startDrag(e, p, src) { e.preventDefault(); dragData = { petal: p, source: src }; const arr = src==='equip'?equipped:inventory, idx=arr.indexOf(p); if(idx>-1) { dragData.originalIndex = idx; arr.splice(idx,1); } setupDragGhost(e, p); }
function setupDragGhost(e, p) { ghost.className = p.type==='mythic'?'mythic-bg':''; ghost.style.background = p.type==='mythic'?'':TIER_COLORS[p.type]; ghost.innerHTML = `${p.dmg}`; ghost.style.display='flex'; ghost.style.left=e.clientX-19+'px'; ghost.style.top=e.clientY-19+'px'; window.addEventListener('mousemove', onDrag); window.addEventListener('mouseup', endDrag); needsUIRender=true; }
function onDrag(e) { ghost.style.left=e.clientX-19+'px'; ghost.style.top=e.clientY-19+'px'; }
function endDrag(e) { ghost.style.display='none'; window.removeEventListener('mousemove', onDrag); window.removeEventListener('mouseup', endDrag); if(!dragData) return; const target=document.elementFromPoint(e.clientX, e.clientY); let droppedInEquip=false; const eqSlots=document.getElementById('equip-grid').children, eqW=document.getElementById('equip-wrapper'); for(let s of eqSlots) if(s.contains(target)||s===target) droppedInEquip=true; if(eqW && (eqW.contains(target)||eqW===target)) droppedInEquip=true; if(droppedInEquip && equipped.length<maxEquipLimit) { if(dragData.source==='equip' && dragData.originalIndex!=null) equipped.splice(Math.min(dragData.originalIndex, equipped.length), 0, dragData.petal); else equipped.push(dragData.petal); } else { if(dragData.source==='inv' && dragData.originalIndex!=null) inventory.splice(Math.min(dragData.originalIndex, inventory.length), 0, dragData.petal); else inventory.push(dragData.petal); } dragData=null; needsUIRender=true; }
function craft(t) { const i=TIERS.indexOf(t); if(i>=TIERS.length-1) return; const count=inventory.filter(p=>p.type===t).length; if(count<5) return; let rm=0; for(let j=inventory.length-1; j>=0&&rm<5; j--) if(inventory[j].type===t) { inventory.splice(j,1); rm++; } if(Math.random()<CRAFT_RATES[t]) inventory.push(createPetal(TIERS[i+1])); else for(let k=0;k<Math.floor(rand(1,5));k++) inventory.push(createPetal(t)); needsUIRender=true; }

// 开发者模式
function clearAllEnemies() { enemies.forEach(e => spawnParticles(e.x, e.y, e.color, 10, 1.5)); enemies =[]; needsUIRender = true; }
function applyDevSettings() { sessionDropMult = parseFloat(document.getElementById('dev-drop').value)||0; sessionCraftMult = parseFloat(document.getElementById('dev-craft').value)||0; maxEquipLimit = Math.max(1, parseInt(document.getElementById('dev-max-equip').value)||12); sessionSpeedMult = parseFloat(document.getElementById('dev-speed').value)||1.0; sessionDmgMult = parseFloat(document.getElementById('dev-dmg').value)||1.0; const newHp = parseFloat(document.getElementById('dev-hp').value); if(!isNaN(newHp) && newHp>=0) player.hp=newHp; sessionHpRegen = parseFloat(document.getElementById('dev-regen').value)||1; sessionXpMult = parseFloat(document.getElementById('dev-xp').value)||1.0; while(equipped.length>maxEquipLimit) inventory.push(equipped.pop()); for(let k in BASE_CRAFT_RATES) CRAFT_RATES[k] = Math.min(1.0, BASE_CRAFT_RATES[k]*sessionCraftMult); needsUIRender=true; closeModal('dev-modal'); }
function resetDevSettings() { document.getElementById('dev-drop').value=1.0; document.getElementById('dev-craft').value=1.0; document.getElementById('dev-max-equip').value=12; document.getElementById('dev-speed').value=1.0; document.getElementById('dev-dmg').value=1.0; document.getElementById('dev-hp').value=player.maxHp; document.getElementById('dev-regen').value=1; document.getElementById('dev-xp').value=1.0; applyDevSettings(); }

// 怪物生成
function spawnEnemy() {
if(LAN.isMultiplayer && !LAN.isHost) return; // 联机非房主不刷怪
const angle = Math.random()*Math.PI*2, maxDim = Math.max(canvas.width, canvas.height), sd = rand(maxDim*0.6, maxDim*1.25), depth = getMapDepth(), lvlMult = player.lvl + (depth*0.5); 
const hp = Math.floor(10 + lvlMult*2 + Math.pow(lvlMult, 1.6)*0.3);
let type = 'basic', color = '#ef4444', roll = Math.random();
if (depth>5 && roll>0.7) { type='bomber'; color='#f97316'; } else if (depth>2 && roll>0.4) { type='dasher'; color='#eab308'; }
let finalSpeed = Math.min((type==='dasher'?1.2:(type==='bomber'?1.4:1.6)) + 2.5*(1-Math.exp(-lvlMult*0.02)), player.baseSpeed*0.85);
enemies.push({ id: Math.random(), x: player.x+Math.cos(angle)*sd, y: player.y+Math.sin(angle)*sd, r: type==='bomber'?18:14, hp, maxHp:hp, speed: finalSpeed, type, color, hitFlash:0, aggro:false, wander:Math.random()*Math.PI*2, dashTimer: rand(60,180), state:'normal' });
}

// ==================== 核心游戏循环 ====================
let lastFrameTime = Date.now();
function loop() {
if(!running) return; requestAnimationFrame(loop);

const now = Date.now();
const dt = Math.min((now - lastFrameTime) / 16.67, 3); // 时间增量，避免大跳跃
lastFrameTime = now;

if (needsUIRender) { renderUINow(); needsUIRender = false; }

const dx = mouseX - canvas.width/2, dy = mouseY - canvas.height/2, mDist = Math.hypot(dx, dy);

if (!isPaused && !LAN.isRoomPaused) {
frame++;

// 更新队友花瓣状态（本地轨道计算）
		if(LAN.isMultiplayer) {
			LAN.updatePetalsState(dt);
			LAN.updatePlayerInterpolation(dt);
		}

// 血清与流血
if(LAN.isMultiplayer) {
LAN.updateSerum(); LAN.applyBleed();
if(LAN.isInjecting) player.baseSpeed *= 0.8; // 减速惩罚 (下方重算时覆盖)
}

// 回血
if (frame % 60 === 0 && sessionHpRegen !== 0 && LAN.bleedStacks === 0) {
if (sessionHpRegen > 0 && player.hp < player.maxHp) { player.hp = Math.min(player.maxHp, player.hp + sessionHpRegen); needsUIRender = true; } 
else if (sessionHpRegen < 0) { player.hp += sessionHpRegen; needsUIRender = true; if(player.hp<=0) gameOver(); }
}

// 导航进度与爆炸波
if (showNav) navProgress += (1 - navProgress) * 0.1; else navProgress += (0 - navProgress) * 0.15;
for(let i=blastWaves.length-1; i>=0; i--) { let b=blastWaves[i]; b.radius+=(b.maxRadius-b.radius)*0.15; b.alpha-=0.03; if(b.alpha<=0 || b.radius>=b.maxRadius-2) blastWaves.splice(i,1); }

// 玩家移动与状态
let speedMultiSum = equipped.reduce((s,p)=>s+TIER_STATS[p.type].speedBonus,0), avgSpeedBonus = equipped.length ? speedMultiSum/equipped.length : 1;
const rot = 0.035 * avgSpeedBonus;
let speedPetalBoost = 0; equipped.forEach(p=>{ if(p.kind==='speed') speedPetalBoost += p.isActive?0.05:0.15; });
let targetRadiusOffset = 0, currentSpeedCap = 3.5 * avgSpeedBonus * (1 + speedPetalBoost) * sessionSpeedMult; 
if(LAN.isInjecting) currentSpeedCap *= 0.8; // 联机注射减速

let dmgMult = 1.0 * sessionDmgMult;
if (isLeftMouseDown) { currentSpeedCap*=1.25; targetRadiusOffset=25; } else if (isRightMouseDown) { targetRadiusOffset=-15; dmgMult*=1.3; }
currentRadiusOffset += (targetRadiusOffset - currentRadiusOffset) * 0.15;
let pRadius = Math.max(25, 45 + Math.min(equipped.length * 1.5, 40) + currentRadiusOffset);

if(mDist > 15) { const a = Math.atan2(dy, dx); const accel = currentSpeedCap * 0.15; player.vx+=Math.cos(a)*accel; player.vy+=Math.sin(a)*accel; }
player.vx*=0.87; player.vy*=0.87;
if(Math.hypot(player.vx,player.vy)>currentSpeedCap) { const s = currentSpeedCap/Math.hypot(player.vx,player.vy); player.vx*=s; player.vy*=s; }
player.x+=player.vx; player.y+=player.vy;

// 玩家碰撞互斥 (联机)
if(LAN.isMultiplayer) {
Object.values(LAN.players).forEach(other => {
let pd = dist(player, other), pr = player.r + 18; // 假定其他玩家r=18
if(pd < pr && pd > 0) {
let ov = pr-pd, nx = (other.x-player.x)/pd, ny = (other.y-player.y)/pd;
player.x -= nx*ov*0.5; player.y -= ny*ov*0.5;
}
});
}

camera.x += (player.x - canvas.width/2 - camera.x) * 0.2; camera.y += (player.y - canvas.height/2 - camera.y) * 0.2;
if (frame % 30 === 0) needsUIRender = true; 
if(frame%20===0 && enemies.length < 35+player.lvl*1.5 && (!LAN.isMultiplayer || LAN.isHost)) spawnEnemy();

player.invincibleTimer=Math.max(0, player.invincibleTimer-1); player.hurtFlashTimer=Math.max(0, player.hurtFlashTimer-1);

// 花瓣轨道计算
equipped.forEach((p,i)=>{
if (!p.isActive && p.kind !== 'missile') { p.cooldownTimer--; if (p.cooldownTimer <= 0) { p.isActive=true; p.durability=p.maxDurability; needsUIRender=true; } } 
else if (!p.isActive && p.kind === 'missile' && p.missileState === 'idle') { p.cooldownTimer--; if (p.cooldownTimer <= 0) { p.isActive=true; p.durability=p.maxDurability; needsUIRender=true; } }

const base=(Math.PI*2/Math.max(1, equipped.length))*i, angle = base+frame*rot, orbitX = player.x+Math.cos(angle)*pRadius, orbitY = player.y+Math.sin(angle)*pRadius;

if (p.kind === 'missile') {
if (p.missileState === 'idle') { p.x = orbitX; p.y = orbitY; if (isLeftMouseDown && p.isActive) { p.missileState = 'flying'; p.missileTimer = 45; const shootAngle = Math.atan2(orbitY-player.y, orbitX-player.x); p.missileVx = Math.cos(shootAngle)*14; p.missileVy = Math.sin(shootAngle)*14; } } 
else if (p.missileState === 'flying') { p.x += p.missileVx; p.y += p.missileVy; p.missileTimer--; if (p.missileTimer <= 0 || !p.isActive) p.missileState = 'returning'; } 
else if (p.missileState === 'returning') { const d = Math.hypot(orbitX-p.x, orbitY-p.y); if (d < 25) { p.missileState='idle'; p.x=orbitX; p.y=orbitY; p.isActive=false; p.cooldownTimer=60; needsUIRender=true; } else { p.x += ((orbitX-p.x)/d)*20; p.y += ((orbitY-p.y)/d)*20; } }
} else { p.x = orbitX; p.y = orbitY; }
});

// 物品拾取
for(let i=groundPetals.length-1;i>=0;i--){
const p=groundPetals[i]; p.life--; const d=dist(player,p);
if(d < 120){ p.attract = Math.min(1.0, (p.attract || 0.04) + 0.04); p.x += (player.x - p.x) * p.attract; p.y += (player.y - p.y) * p.attract; }
if(d<25 || p.life<=0){ if(p.life>0) { inventory.push(p); spawnDamageText(p.x, p.y, "+物品", "#38bdf8"); } groundPetals.splice(i,1); needsUIRender=true; }
}

// 怪物逻辑与碰撞
for(let i=0; i<enemies.length; i++) {
for(let j=i+1; j<enemies.length; j++) { const d = dist(enemies[i], enemies[j]), md=enemies[i].r+enemies[j].r; if(d<md && d>0) { const ov=md-d, nx=(enemies[j].x-enemies[i].x)/d, ny=(enemies[j].y-enemies[i].y)/d; enemies[i].x-=nx*ov*0.5; enemies[i].y-=ny*ov*0.5; enemies[j].x+=nx*ov*0.5; enemies[j].y+=ny*ov*0.5; } }
const ep = enemies[i], dP = dist(ep, player), minP = ep.r + player.r;
if(dP < minP && dP > 0) { const ov=minP-dP, nx=(player.x-ep.x)/dP, ny=(player.y-ep.y)/dP; ep.x-=nx*ov*0.5; ep.y-=ny*ov*0.5; player.x+=nx*ov*0.5; player.y+=ny*ov*0.5; }
}

for(let i=enemies.length-1;i>=0;i--){
const e=enemies[i];
if (e.hp <= 0) { player.xp += (2+Math.floor(player.lvl/4))*sessionXpMult; if (Math.random()<0.5) dropGroundPetal(e.x, e.y); spawnParticles(e.x, e.y, e.color, 10, 1.5); enemies.splice(i, 1); if(player.xp >= player.xpMax) { player.lvl++; player.xp=0; player.xpMax=Math.floor(player.xpMax*1.35); player.maxHp+=15; spawnDamageText(player.x, player.y - 30, "LEVEL UP!", "#facc15"); screenShake = 10; } needsUIRender=true; continue; }
const edx=player.x-e.x, edy=player.y-e.y, edist=Math.hypot(edx,edy)||1;

if(!LAN.isMultiplayer || LAN.isHost) {
if(!e.aggro && edist<=Math.max(canvas.width,canvas.height)*0.5) e.aggro=true;
if(e.aggro){ let spd=e.speed; if (e.type==='dasher') { e.dashTimer--; if (e.dashTimer<=0) { e.state='dashing'; e.dashTimer=rand(80,150); } if (e.state==='dashing' && e.dashTimer>130) { spd*=3.5; spawnParticles(e.x,e.y,'#eab308',1); } else e.state='normal'; } e.x+=(edx/edist)*spd; e.y+=(edy/edist)*spd; }
else { const wa=e.wander+Math.sin(frame*0.05+i*0.1)*0.5; e.x+=Math.cos(wa)*e.speed*0.12; e.y+=Math.sin(wa)*e.speed*0.12; }
if(edist>Math.max(canvas.width,canvas.height)*2.0){ enemies.splice(i,1); continue; }
}

e.hitFlash=Math.max(0,e.hitFlash-1);
let died = false;

// 花瓣打怪
for(let p of equipped){
if(p.isActive && collides(p,e)){
let actualDmg = p.dmg * dmgMult;
if (p.kind === 'missile' && p.missileState === 'flying') actualDmg = 999999;
if (p.kind === 'explosive') actualDmg *= 2.5;
e.hp -= actualDmg; e.hitFlash = 5; p.durability -= 1; 
spawnDamageText(e.x, e.y, Math.floor(actualDmg>10000?e.maxHp:actualDmg), e.hp<=0?'#facc15':'#fff'); spawnParticles(e.x, e.y, getPetalColor(p.type), 3);
if (p.kind === 'explosive') { blastWaves.push({x:e.x,y:e.y,radius:10,maxRadius:90,color:'#f97316',alpha:0.7,type:'normal'}); spawnParticles(e.x,e.y,'#f97316',20,4); enemies.forEach(o=>{if(o!==e && dist(e,o)<90){ o.hp-=actualDmg*1.5; o.hitFlash=5; spawnDamageText(o.x,o.y,Math.floor(actualDmg*1.5),'#f97316'); }}); p.durability = 0; }
if(p.durability <= 0) { p.isActive = false; p.cooldownTimer = TIER_STATS[p.type].regen; } 
if(e.hp <= 0) { died = true; break; }
}
}

// 联机友伤 (花瓣打人) - 仅对玩家生效，不影响怪物
if(LAN.isMultiplayer) {
if(!LAN._ffCooldown) LAN._ffCooldown = {};
var ffNow = Date.now();
var playerIds = Object.keys(LAN.players);
for (var pi = 0; pi < playerIds.length; pi++) {
var playerId = playerIds[pi];
var other = LAN.players[playerId];
if(other.hp !== undefined && other.hp <= 0) continue;
for(var pi2 = 0; pi2 < equipped.length; pi2++) {
var p = equipped[pi2];
if(p.isActive && dist(p, other) < p.r + 18) {
var cdKey = p.id + '|' + playerId;
if (LAN._ffCooldown[cdKey] && (now - LAN._ffCooldown[cdKey]) < 300) continue;
LAN._ffCooldown[cdKey] = now;

var ffDmg = TIER_STATS[p.type].ffDmg;
var isMissileFlying = p.kind === 'missile' && p.missileState === 'flying';
var isSlashActive = isLeftMouseDown;
var isBleedHit = isMissileFlying && isSlashActive;

other.hp -= ffDmg;
LAN.sendDamageToServer(playerId, ffDmg, isBleedHit, 5);

if(isBleedHit) {
LAN.addBleed(1);
spawnDamageText(other.x, other.y, '-' + ffDmg + ' \u6d41\u8840', '#991b1b');
} else {
spawnDamageText(other.x, other.y, '-' + ffDmg, '#ff6600');
}

p.durability -= 1;
if(p.durability <= 0) { p.isActive = false; p.cooldownTimer = TIER_STATS[p.type].regen; }
spawnParticles(other.x, other.y, getPetalColor(p.type), 3);
}
}
}
}

if(died) {
player.xp += (2+Math.floor(player.lvl/4))*sessionXpMult;
if (Math.random()<0.5) dropGroundPetal(e.x, e.y);
spawnParticles(e.x, e.y, e.color, 10, 1.5);
enemies.splice(i, 1);
if(player.xp >= player.xpMax) { player.lvl++; player.xp=0; player.xpMax=Math.floor(player.xpMax*1.35); player.maxHp+=15; spawnDamageText(player.x, player.y-30, "LEVEL UP!", "#facc15"); screenShake=10; }
needsUIRender=true; continue;
}

// 怪物打人
if(player.invincibleTimer <= 0 && collides(player,e)){
let dmgTaken = 8;
if (e.type === 'bomber') { dmgTaken=25; spawnParticles(e.x,e.y,'#f97316',30,4); screenShake=15; e.hp=0; } 
else { e.hp-=2; e.hitFlash=4; e.x-=(edx/edist)*25; e.y-=(edy/edist)*25; screenShake=5; }

if (player.hp - dmgTaken <= 0) {
let gIdx = equipped.findIndex(pt => pt.kind==='guardian' && pt.isActive);
if (gIdx !== -1) { equipped.splice(gIdx,1); dmgTaken=0; player.hp=player.maxHp; player.invincibleTimer=120; spawnDamageText(player.x,player.y,"守护触发!", "#facc15"); equipped.forEach(pt=>{pt.isActive=false; pt.cooldownTimer=Math.max(60,TIER_STATS[pt.type].regen); pt.durability=0; if(pt.kind==='missile')pt.missileState='idle';}); blastWaves.push({x:player.x,y:player.y,radius:10,maxRadius:200,color:'#facc15',alpha:0.8,type:'guardian'}); enemies.forEach(o=>{if(dist(player,o)<200){ o.hp-=500; o.hitFlash=10; spawnDamageText(o.x,o.y,"500",'#ffffff'); }}); needsUIRender=true; continue; }
}
player.hp -= dmgTaken; 
if (dmgTaken > 0) { player.invincibleTimer = 35; player.hurtFlashTimer = 8; spawnDamageText(player.x, player.y, `-${dmgTaken}`, '#ef4444'); }
if(player.hp <= 0) gameOver(false); needsUIRender = true;
}
}
for(let i=particles.length-1;i>=0;i--){ const p=particles[i]; p.x+=p.vx; p.y+=p.vy; p.life--; p.vx*=0.9; p.vy*=0.9; if(p.life<=0) particles.splice(i,1); }
for(let i=damageTexts.length-1;i>=0;i--){ const dt = damageTexts[i]; dt.y += dt.vy; dt.life--; if(dt.life<=0) damageTexts.splice(i,1); }
}
draw(mDist);
}

function gameOver(isBleedKill = false) {
running = false; 
if(LAN.isMultiplayer) {
document.getElementById('multiplayerRevive').classList.add('visible');
} else {
document.getElementById('gameOver').classList.add('visible');
document.getElementById('finalLvl').textContent = player.lvl;
document.getElementById('survTime').textContent = Math.floor((Date.now()-startTime)/1000);
document.getElementById('finalPos').textContent = getMapDepth();
}
}

// ==================== 绘制系统 ====================
function draw(mouseDist) {
ctx.clearRect(0,0,canvas.width,canvas.height); ctx.save();
if (screenShake > 0 && !isPaused && !LAN.isRoomPaused) { ctx.translate(rand(-screenShake, screenShake), rand(-screenShake, screenShake)); screenShake *= 0.85; if (screenShake < 0.5) screenShake = 0; }

const gs=60, offX=-(camera.x%gs), offY=-(camera.y%gs);
ctx.strokeStyle='#334155'; ctx.lineWidth=1; ctx.globalAlpha=0.25;
for(let x=offX-gs;x<canvas.width+gs;x+=gs){ ctx.beginPath(); ctx.moveTo(x,0); ctx.lineTo(x,canvas.height); ctx.stroke(); }
for(let y=offY-gs;y<canvas.height+gs;y+=gs){ ctx.beginPath(); ctx.moveTo(0,y); ctx.lineTo(canvas.width,y); ctx.stroke(); }
ctx.globalAlpha=1;

if (mouseDist > 20 && !isPaused && !LAN.isRoomPaused) {
const angle = Math.atan2(mouseY - canvas.height/2, mouseX - canvas.width/2);
const startX = canvas.width/2 + Math.cos(angle)*22, startY = canvas.height/2 + Math.sin(angle)*22, len = mouseDist - 22;
ctx.save(); ctx.translate(startX, startY); ctx.rotate(angle); ctx.strokeStyle = 'rgba(200, 215, 240, 0.25)'; ctx.lineWidth = 2.5; ctx.lineCap = 'round';
ctx.beginPath(); ctx.moveTo(0, 0); ctx.lineTo(len, 0); ctx.moveTo(len, 0); ctx.lineTo(len-9, -5); ctx.moveTo(len, 0); ctx.lineTo(len-9, 5); ctx.stroke(); ctx.restore();
}

ctx.translate(-camera.x,-camera.y);

if (navProgress > 0.01) {
ctx.save(); ctx.strokeStyle = `rgba(167, 139, 250, ${0.6 * navProgress})`; ctx.lineWidth = 2; ctx.setLineDash([10, 10]); ctx.lineDashOffset = -frame * 2; 
ctx.beginPath(); ctx.moveTo(player.x, player.y); ctx.lineTo(player.x+(0-player.x)*navProgress, player.y+(0-player.y)*navProgress); ctx.stroke();
ctx.globalAlpha = navProgress; ctx.strokeStyle = '#a78bfa'; ctx.setLineDash([]); ctx.lineWidth = 2;
ctx.beginPath(); ctx.moveTo(-15, 0); ctx.lineTo(15, 0); ctx.moveTo(0, -15); ctx.lineTo(0, 15); ctx.stroke();
ctx.beginPath(); ctx.arc(0, 0, 8, 0, Math.PI*2); ctx.stroke();
ctx.translate(player.x+(0-player.x)*navProgress*0.5, player.y+(0-player.y)*navProgress*0.5);
let navAngle = Math.atan2(-player.y, -player.x); if (navAngle > Math.PI/2 || navAngle < -Math.PI/2) navAngle += Math.PI; ctx.rotate(navAngle);
ctx.fillStyle = `rgba(167, 139, 250, ${navProgress})`; ctx.font = 'bold 16px system-ui'; ctx.textAlign = 'center'; ctx.textBaseline = 'bottom'; ctx.shadowColor = 'rgba(0,0,0,0.8)'; ctx.shadowBlur = 4; ctx.fillText(`${getMapDepth()}m`, 0, -6); ctx.restore();
}

blastWaves.forEach(b => { ctx.save(); ctx.globalAlpha = Math.max(0, b.alpha); ctx.beginPath(); ctx.arc(b.x, b.y, b.radius, 0, Math.PI*2); if(b.type === 'guardian') { ctx.fillStyle = b.color; ctx.fill(); } else { ctx.lineWidth = 4 + (b.alpha * 5); ctx.strokeStyle = b.color; ctx.fillStyle = 'rgba(249, 115, 22, 0.15)'; ctx.fill(); ctx.stroke(); } ctx.restore(); });

groundPetals.forEach(p=>{ ctx.globalAlpha = p.life<100 ? p.life/100 : 0.8; const col = getPetalColor(p.type, p.x+p.y); ctx.shadowBlur=10; ctx.shadowColor=col; ctx.fillStyle=col; ctx.beginPath(); ctx.ellipse(p.x,p.y,8,5, Math.atan2(p.y-player.y,p.x-player.x),0,Math.PI*2); ctx.fill(); ctx.shadowBlur=0; ctx.strokeStyle='#fff'; ctx.lineWidth=1.5; ctx.stroke(); });
ctx.globalAlpha=1;

enemies.forEach(e=>{
ctx.fillStyle = e.aggro ? (e.hitFlash>0?'#fff': e.color) : '#64748b'; ctx.beginPath(); ctx.arc(e.x,e.y,e.r,0,Math.PI*2); ctx.fill();
if (e.type === 'bomber' && e.aggro) { ctx.fillStyle = '#fff'; ctx.beginPath(); ctx.arc(e.x,e.y,e.r*0.5,0,Math.PI*2); ctx.fill(); }
ctx.fillStyle='#0f172a'; ctx.fillRect(e.x-12,e.y-e.r-8,24,4); ctx.fillStyle='#22c55e'; ctx.fillRect(e.x-12,e.y-e.r-8,24*Math.max(0,e.hp/e.maxHp),4);
});
// 渲染联机玩家
if(LAN.isMultiplayer) {
for (const playerId in LAN.players) {
const other = LAN.players[playerId];
if(other.hp !== undefined && other.hp <= 0) continue; 
const petalsState = LAN.playerPetalsState[playerId];
// 画他人的花瓣
if (petalsState && petalsState.petals.length > 0) {
petalsState.petals.forEach((petalState, i) => {
const petalData = other.petals[i];
if (!petalData) return;
// 计算世界坐标
const pos = LAN.getPetalWorldPosition(playerId, i);
if (!pos) return;
if(!petalData.isActive) {
// 非活动花瓣显示
ctx.globalAlpha=0.25;
ctx.strokeStyle='#64748b';
ctx.lineWidth=2;
ctx.setLineDash([4,4]);
ctx.beginPath();
ctx.ellipse(pos.x, pos.y, 10, 6, pos.angle, 0, Math.PI*2);
ctx.stroke();
ctx.setLineDash([]);
ctx.globalAlpha=1;
} else {
// 活动花瓣显示
const grad = ctx.createRadialGradient(pos.x, pos.y, 0, pos.x, pos.y, 10);
grad.addColorStop(0, '#ffffff');
grad.addColorStop(1, getPetalColor(petalData.type || 'common', i * 20));
ctx.fillStyle = grad;
ctx.globalAlpha = 0.8;
ctx.beginPath();
ctx.ellipse(pos.x, pos.y, 10, 6, pos.angle, 0, Math.PI*2);
ctx.fill();
ctx.strokeStyle='#fff';
ctx.lineWidth=1.5;
ctx.stroke();
ctx.globalAlpha=1;
}
});
}
ctx.globalAlpha=1;
ctx.fillStyle = other.isBleeding ? '#dc2626' : '#a855f7';
ctx.beginPath(); ctx.arc(other.x, other.y, 18, 0, Math.PI*2); ctx.fill();
ctx.strokeStyle='#fff'; ctx.lineWidth=2; ctx.stroke();
ctx.fillStyle = '#fff'; ctx.font = '10px sans-serif'; ctx.textAlign='center'; ctx.fillText(other.name, other.x, other.y - 25);
}
}
equipped.forEach((p, i)=>{
if(!p.isActive && p.kind !== 'missile') { ctx.globalAlpha=0.25; ctx.strokeStyle='#64748b'; ctx.lineWidth=2; ctx.setLineDash([4,4]); ctx.beginPath(); ctx.ellipse(p.x,p.y,10,6,Math.atan2(p.y-player.y,p.x-player.x),0,Math.PI*2); ctx.stroke(); ctx.setLineDash([]); ctx.globalAlpha=1; return; }
if(!p.isActive && p.kind === 'missile' && p.missileState === 'idle') { ctx.globalAlpha=0.25; ctx.strokeStyle='#64748b'; ctx.lineWidth=2; ctx.setLineDash([4,4]); ctx.beginPath(); ctx.ellipse(p.x,p.y,10,6,Math.atan2(p.y-player.y,p.x-player.x),0,Math.PI*2); ctx.stroke(); ctx.setLineDash([]); ctx.globalAlpha = 1; return; }

const grad = ctx.createRadialGradient(p.x, p.y, 0, p.x, p.y, 10);
grad.addColorStop(0, '#ffffff');
grad.addColorStop(1, getPetalColor(p.type, i * 20));
ctx.fillStyle = grad;
ctx.globalAlpha = 0.6 + (p.durability/p.maxDurability)*0.4;
ctx.beginPath(); ctx.ellipse(p.x,p.y,10,6,Math.atan2(p.y-player.y,p.x-player.x),0,Math.PI*2); ctx.fill();
ctx.strokeStyle='#fff'; ctx.lineWidth=1.5; ctx.stroke(); ctx.globalAlpha = 1;
});
// 绘制玩家本体（与 florr.html 一致：蓝色外圈 + 黄色/绿色花蕊）
ctx.save(); ctx.translate(player.x, player.y);
if (player.hurtFlashTimer > 0) { ctx.fillStyle = '#ef4444'; ctx.shadowBlur = 15; ctx.shadowColor = '#ef4444'; }
else { ctx.fillStyle = '#38bdf8'; ctx.shadowBlur = 10; ctx.shadowColor = '#38bdf8'; }
ctx.beginPath(); ctx.arc(0, 0, player.r, 0, Math.PI*2); ctx.fill(); ctx.shadowBlur = 0;
// 无敌时描边闪烁，否则用标准蓝色描边
ctx.strokeStyle = player.invincibleTimer > 0 ? `rgba(255,255,255,${0.4 + Math.sin(frame * 0.2) * 0.3})` : '#0ea5e9';
ctx.lineWidth = 3; ctx.stroke();
// 中心花蕊：注射血清时变绿，否则标准黄色
ctx.fillStyle = LAN.isInjecting ? '#4ade80' : '#fef08a';
ctx.beginPath(); ctx.arc(0, 0, 7, 0, Math.PI*2); ctx.fill();
if(LAN.bleedStacks > 0) {
ctx.strokeStyle = 'rgba(220, 38, 38, 0.7)';
ctx.lineWidth = 3;
ctx.beginPath(); ctx.arc(0, 0, player.r + 4, 0, Math.PI*2); ctx.setLineDash([6, 4]); ctx.lineDashOffset = -frame * 2; ctx.stroke(); ctx.setLineDash([]);
var bleedDripCount = Math.min(LAN.bleedStacks, 5);
for(var bd = 0; bd < bleedDripCount; bd++) {
var dripAngle = (frame * 0.05 + bd * Math.PI * 2 / bleedDripCount) % (Math.PI * 2);
var dripDist = player.r + 8 + Math.sin(frame * 0.1 + bd) * 6;
ctx.fillStyle = '#991b1b';
ctx.globalAlpha = 0.5 + Math.sin(frame * 0.15 + bd) * 0.3;
ctx.beginPath(); ctx.arc(Math.cos(dripAngle) * dripDist, Math.sin(dripAngle) * dripDist, 3, 0, Math.PI*2); ctx.fill();
}
ctx.globalAlpha = 1;
}
ctx.restore();
// 渲染死亡粒子
particles.forEach(p=>{ ctx.globalAlpha=p.life/35; ctx.fillStyle=p.color; ctx.beginPath(); ctx.arc(p.x,p.y,p.r,0,Math.PI*2); ctx.fill(); });
ctx.globalAlpha=1;
// 渲染伤害/拾取浮字（+物品、伤害数字、LEVEL UP等）
ctx.font = "bold 16px system-ui";
ctx.textAlign = "center";
ctx.textBaseline = "alphabetic";
damageTexts.forEach(dt => {
ctx.globalAlpha = dt.life / dt.maxLife;
ctx.fillStyle = dt.color;
ctx.shadowColor = "rgba(0,0,0,0.8)";
ctx.shadowBlur = 4;
ctx.fillText(dt.text, dt.x, dt.y);
});
ctx.shadowBlur = 0; ctx.globalAlpha = 1;
ctx.restore(); // 恢复 translate(-camera.x, -camera.y) 之前的状态
}
// ==================== 游戏启动与全局初始化 ====================
// 确保页面加载后立即进入主循环
window.onload = () => {
// 自动扫描房间列表的简易定时器（针对联机版）
if (typeof LAN !== 'undefined') {
setInterval(() => {
// 这里可以添加局域网房间自动扫描逻辑
// 目前通过 UI 手动刷新或后端 Beacon 推送更新 lan-room-list 元素
}, 3000);
}
// 执行第一次 UI 渲染
renderUINow();
// 启动引擎
loop();
};
// 辅助：处理窗口失焦自动暂停
window.addEventListener('blur', () => {
if (!LAN.isMultiplayer && running && !isPaused) {
isPausedBySpace = true;
updatePauseState();
}
});
</script>
</body>
</html>
```