<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初悅 · Chuyue 文字人生</title>
    <!-- 字體預載 (四種風格) -->
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@400;600&family=Cormorant+Garamond:wght@400;600&family=Playfair+Display:wght@400;600&family=Noto+Sans+TC:wght@300;400;500&display=swap" rel="stylesheet">
    <!-- Tailwind (輕量) -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome 圖標 -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <!-- 自定義樣式 -->
    <style>
        /* 字體家族變量 */
        .font-cinzel { font-family: 'Cinzel', 'Noto Sans TC', serif; }
        .font-cormorant { font-family: 'Cormorant Garamond', 'Noto Sans TC', serif; }
        .font-playfair { font-family: 'Playfair Display', 'Noto Sans TC', serif; }
        .font-noto { font-family: 'Noto Sans TC', sans-serif; }

        /* 主題色系統 (CSS 變量) */
        :root {
            --bg-primary: #f5f0eb;
            --bg-secondary: #e8e0d5;
            --card-bg: rgba(255, 255, 255, 0.7);
            --text-primary: #2c2c2c;
            --text-secondary: #5c5c5c;
            --accent: #c4a484;
            --border: #d4c8bc;
        }
        /* 少女元氣 */
        .theme-sakura {
            --bg-primary: #fce4e9;
            --bg-secondary: #f8d5dd;
            --card-bg: rgba(255, 240, 245, 0.8);
            --accent: #e6a6ac;
            --text-primary: #4a3b3f;
            --border: #eac0c7;
        }
        /* 橘子汽水 */
        .theme-orange {
            --bg-primary: #fff3e0;
            --bg-secondary: #ffe4cc;
            --card-bg: rgba(255, 248, 235, 0.8);
            --accent: #ff8a5c;
            --text-primary: #3d2e24;
            --border: #ffc9a9;
        }
        /* 黑金尊貴 */
        .theme-black-gold {
            --bg-primary: #1a1a1a;
            --bg-secondary: #2a2a2a;
            --card-bg: rgba(40, 40, 40, 0.9);
            --accent: #c6a15b;
            --text-primary: #f0e6d2;
            --text-secondary: #b8aa9a;
            --border: #5f4e3c;
        }

        body {
            background-color: var(--bg-primary);
            color: var(--text-primary);
            transition: background-color 0.3s, color 0.3s;
            min-height: 100vh;
            font-family: 'Noto Sans TC', sans-serif;
        }
        .game-container {
            background: var(--bg-secondary);
            border-color: var(--border);
        }
        .card {
            background: var(--card-bg);
            backdrop-filter: blur(8px);
            border: 1px solid var(--border);
            border-radius: 1rem;
            box-shadow: 0 8px 20px rgba(0,0,0,0.1);
        }
        .btn-accent {
            background: var(--accent);
            color: white;
            transition: 0.2s;
        }
        .btn-accent:hover {
            filter: brightness(0.9);
        }
        /* 聊天氣泡 */
        .bubble-assistant {
            background: var(--accent);
            color: white;
            border-radius: 18px 18px 18px 4px;
        }
        .bubble-user {
            background: var(--bg-secondary);
            border: 1px solid var(--border);
            border-radius: 18px 18px 4px 18px;
        }
        /* 滾動條美化 */
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: var(--bg-secondary); }
        ::-webkit-scrollbar-thumb { background: var(--accent); border-radius: 4px; }
    </style>
</head>
<body class="antialiased">
    
<div id="app" class="min-h-screen p-4 md:p-6 transition-all"></div>

<!-- React & 核心庫 -->
<script src="https://cdn.jsdelivr.net/npm/react@18.2.0/umd/react.development.js"></script>
<script src="https://cdn.jsdelivr.net/npm/react-dom@18.2.0/umd/react-dom.development.js"></script>
<script src="https://cdn.jsdelivr.net/npm/babel-standalone@6.26.0/babel.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lucide@0.263.1/dist/umd/lucide.min.js"></script>

<script type="text/babel">
// ---------- 工具函數 ----------
const { useState, useEffect, useCallback } = React;

// 默認 AI 配置 (OpenAI 兼容)
const DEFAULT_API = {
    endpoint: 'https://api.openai.com/v1/chat/completions',
    model: 'gpt-3.5-turbo',
    key: '',
    temperature: 0.85,
    maxTokens: 800
};

// 世界書範例結構
const DEFAULT_WORLD = {
    name: '現代都市',
    era: '2024年 台北',
    theme: '都市奮鬥',
    rules: '現實世界，社交為核心',
    special: '可以透過努力改變命運',
    atmosphere: '溫暖現實主義'
};

// ---------- 主應用 ----------
function App() {
    // 核心狀態
    const [step, setStep] = useState('welcome'); // welcome, api, world, game
    const [apiConfig, setApiConfig] = useState(DEFAULT_API);
    const [worldBook, setWorldBook] = useState(DEFAULT_WORLD);
    const [theme, setTheme] = useState('sakura'); // sakura, orange, black-gold
    const [font, setFont] = useState('noto'); // noto, cinzel, cormorant, playfair
    const [lang, setLang] = useState('zh-TW'); // zh-TW, zh-CN, en, ja, ko
    
    // 遊戲數據
    const [player, setPlayer] = useState({
        name: '旅人',
        age: 22,
        gender: 'female',
        location: '台北市',
        money: 12500,
        health: 85,
        energy: 70,
        mood: 65,
        iq: 60,
        charm: 55
    });
    
    const [story, setStory] = useState([]);
    const [choices, setChoices] = useState([]);
    const [inventory, setInventory] = useState([
        { id: 1, name: '手機', icon: '📱' },
        { id: 2, name: '錢包', icon: '👛' }
    ]);
    const [npcs, setNpcs] = useState([]);
    const [chatNpc, setChatNpc] = useState(null);
    const [shopItems, setShopItems] = useState([]);
    const [schedule, setSchedule] = useState([]);
    const [isAiThinking, setIsAiThinking] = useState(false);
    
    // 世界書導入
    const handleWorldImport = (e) => {
        const file = e.target.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = (ev) => {
            try {
                const json = JSON.parse(ev.target.result);
                setWorldBook(prev => ({ ...prev, ...json }));
                alert('世界書導入成功！');
            } catch (err) {
                alert('JSON 格式錯誤');
            }
        };
        reader.readAsText(file);
    };
    
    // AI 調用 (模擬)
    const callAI = useCallback(async (prompt, schema = null) => {
        if (!apiConfig.key && !apiConfig.endpoint.includes('localhost')) {
            alert('請先設定 API Key');
            return null;
        }
        setIsAiThinking(true);
        try {
            // 這裡是實際 API 調用，目前使用模擬延遲
            await new Promise(r => setTimeout(r, 800));
            
            // 模擬 AI 回應 (實際使用時替換為 fetch)
            const mockResponse = {
                narrative: '你走在繁華的街道上，突然看到一個熟悉的身影...',
                choices: [
                    { id: 'c1', text: '上前打招呼', hint: '可能認識新朋友', type: 'social' },
                    { id: 'c2', text: '假裝沒看見', hint: '避免尷尬', type: 'action' },
                    { id: 'c3', text: '買杯咖啡', hint: '恢復精力 +5', type: 'rest' }
                ],
                statChanges: { energy: -2, mood: +3 },
                newEvent: '街角偶遇'
            };
            
            if (schema?.type === 'opening') {
                return {
                    opening: '故事開始於一個陽光明媚的早晨...',
                    sceneTitle: '嶄新的一天',
                    choices: mockResponse.choices,
                    initialNpcs: [
                        { name: '小美', age: 24, occupation: '設計師', relationship: '朋友' }
                    ]
                };
            }
            return mockResponse;
        } finally {
            setIsAiThinking(false);
        }
    }, [apiConfig]);
    
    // 開始遊戲 (生成開場)
    const startGame = async () => {
        const result = await callAI({
            role: 'system',
            content: `世界書: ${JSON.stringify(worldBook)}，玩家: ${player.name}，生成開場`
        }, { type: 'opening' });
        
        if (result) {
            setStory([{ role: 'assistant', content: result.opening, timestamp: Date.now() }]);
            setChoices(result.choices || []);
            if (result.initialNpcs) setNpcs(result.initialNpcs);
            setStep('game');
        }
    };
    
    // 選擇選項
    const handleChoice = async (choice) => {
        const newStory = [...story, { role: 'user', content: choice.text, timestamp: Date.now() }];
        setStory(newStory);
        
        const result = await callAI(`玩家選擇了: ${choice.text}，根據世界書${worldBook.name}推進故事`);
        
        if (result) {
            setStory([...newStory, { role: 'assistant', content: result.narrative, timestamp: Date.now() }]);
            setChoices(result.choices || []);
            
            // 更新玩家屬性
            if (result.statChanges) {
                setPlayer(prev => {
                    const newStats = { ...prev };
                    Object.keys(result.statChanges).forEach(k => {
                        if (k in prev) newStats[k] = Math.max(0, Math.min(100, prev[k] + result.statChanges[k]));
                    });
                    return newStats;
                });
            }
            
            // 更新購物系統 (隨機生成商品)
            if (Math.random() > 0.7) {
                setShopItems([
                    { id: 1, name: '咖啡', price: 120, effect: '精力+10' },
                    { id: 2, name: '書本', price: 350, effect: '智力+5' },
                    { id: 3, name: '禮物', price: 500, effect: '好感度上升' }
                ]);
            }
            
            // 更新日程 (隨機)
            if (Math.random() > 0.6) {
                const hours = ['上午', '下午', '晚上'];
                setSchedule(hours.map(h => ({
                    time: h,
                    event: result.newEvent || '自由時間'
                })));
            }
        }
    };
    
    // 自由輸入
    const handleFreeInput = async (text) => {
        if (!text.trim()) return;
        const newStory = [...story, { role: 'user', content: text, timestamp: Date.now() }];
        setStory(newStory);
        
        const result = await callAI(`玩家自由行動: ${text}`);
        if (result) {
            setStory([...newStory, { role: 'assistant', content: result.narrative, timestamp: Date.now() }]);
            setChoices(result.choices || []);
        }
    };
    
    // 重置所有 (讓 AI 重新生成)
    const refreshAll = () => {
        callAI('根據當前世界書和玩家狀態，重新生成完整的場景、選項、商店、日程和NPC互動');
    };
    
    // 主題對應的 body class
    useEffect(() => {
        document.body.className = `theme-${theme} font-${font}`;
    }, [theme, font]);
    
    // ---------- 歡迎/設定頁面 ----------
    if (step === 'welcome') {
        return (
            <div className="min-h-screen flex items-center justify-center p-4">
                <div className="card max-w-3xl w-full p-8 space-y-6">
                    <h1 className="text-4xl font-bold text-center" style={{ color: 'var(--accent)' }}>初悅 · Chuyue</h1>
                    <p className="text-center opacity-80">你的AI文字人生模擬器</p>
                    
                    <div className="grid md:grid-cols-2 gap-6 pt-4">
                        {/* 左側：API 設定 */}
                        <div className="space-y-4">
                            <h2 className="text-xl font-semibold flex items-center gap-2"><i className="fas fa-plug"></i> AI 接點</h2>
                            <input type="text" placeholder="API Endpoint" className="w-full p-2 rounded bg-white/50 border" 
                                value={apiConfig.endpoint} onChange={e => setApiConfig({...apiConfig, endpoint: e.target.value})} />
                            <input type="text" placeholder="API Key" className="w-full p-2 rounded bg-white/50 border" 
                                value={apiConfig.key} onChange={e => setApiConfig({...apiConfig, key: e.target.value})} />
                            <input type="text" placeholder="Model (ex: gpt-3.5-turbo)" className="w-full p-2 rounded bg-white/50 border" 
                                value={apiConfig.model} onChange={e => setApiConfig({...apiConfig, model: e.target.value})} />
                            <div className="flex gap-2">
                                <input type="number" placeholder="溫度" className="w-1/2 p-2 rounded bg-white/50 border" 
                                    value={apiConfig.temperature} onChange={e => setApiConfig({...apiConfig, temperature: +e.target.value})} />
                                <input type="number" placeholder="最大Token" className="w-1/2 p-2 rounded bg-white/50 border" 
                                    value={apiConfig.maxTokens} onChange={e => setApiConfig({...apiConfig, maxTokens: +e.target.value})} />
                            </div>
                        </div>
                        
                        {/* 右側：世界書導入 */}
                        <div className="space-y-4">
                            <h2 className="text-xl font-semibold flex items-center gap-2"><i className="fas fa-book-open"></i> 世界書</h2>
                            <div className="border-2 border-dashed rounded-lg p-4 text-center" style={{ borderColor: 'var(--border)' }}>
                                <input type="file" accept=".json" onChange={handleWorldImport} className="hidden" id="world-upload" />
                                <label htmlFor="world-upload" className="cursor-pointer block">
                                    <i className="fas fa-cloud-upload-alt text-3xl mb-2 opacity-60"></i>
                                    <p>點擊上傳 JSON 世界書</p>
                                    <p className="text-sm opacity-50">或使用默認世界</p>
                                </label>
                            </div>
                            <div className="bg-black/5 p-3 rounded">
                                <p className="font-medium">當前世界: {worldBook.name}</p>
                                <p className="text-sm truncate">{worldBook.theme} · {worldBook.era}</p>
                            </div>
                        </div>
                    </div>
                    
                    {/* 主題/字體/語言 */}
                    <div className="grid grid-cols-3 gap-3 pt-2">
                        <select className="p-2 rounded bg-white/50 border" value={theme} onChange={e => setTheme(e.target.value)}>
                            <option value="sakura">🌸 少女元氣</option>
                            <option value="orange">🍊 橘子汽水</option>
                            <option value="black-gold">🖤 黑金尊貴</option>
                        </select>
                        <select className="p-2 rounded bg-white/50 border" value={font} onChange={e => setFont(e.target.value)}>
                            <option value="noto">📝 現代簡約</option>
                            <option value="cinzel">👑 古典優雅</option>
                            <option value="cormorant">📜 浪漫文學</option>
                            <option value="playfair">🖋️ 經典襯線</option>
                        </select>
                        <select className="p-2 rounded bg-white/50 border" value={lang} onChange={e => setLang(e.target.value)}>
                            <option value="zh-TW">繁體中文</option>
                            <option value="zh-CN">简体中文</option>
                            <option value="en">English</option>
                            <option value="ja">日本語</option>
                            <option value="ko">한국어</option>
                        </select>
                    </div>
                    
                    {/* 簡單角色設定 */}
                    <div className="grid grid-cols-2 gap-3 pt-2">
                        <input type="text" placeholder="角色名稱" className="p-2 rounded bg-white/50 border"
                            value={player.name} onChange={e => setPlayer({...player, name: e.target.value})} />
                        <select className="p-2 rounded bg-white/50 border" value={player.gender} onChange={e => setPlayer({...player, gender: e.target.value})}>
                            <option value="female">女性</option>
                            <option value="male">男性</option>
                            <option value="other">其他</option>
                        </select>
                    </div>
                    
                    <button onClick={() => setStep('api')} className="btn-accent w-full py-3 rounded-lg text-lg font-semibold">
                        下一步：確認設定
                    </button>
                </div>
            </div>
        );
    }
    
    // API 確認頁
    if (step === 'api') {
        return (
            <div className="min-h-screen flex items-center justify-center p-4">
                <div className="card max-w-md w-full p-8 space-y-5">
                    <h2 className="text-2xl font-bold">確認 AI 接點</h2>
                    <div className="space-y-2 opacity-80">
                        <p><span className="font-medium">Endpoint:</span> {apiConfig.endpoint}</p>
                        <p><span className="font-medium">Model:</span> {apiConfig.model}</p>
                        <p><span className="font-medium">世界書:</span> {worldBook.name}</p>
                    </div>
                    <button onClick={startGame} className="btn-accent w-full py-3 rounded-lg" disabled={isAiThinking}>
                        {isAiThinking ? 'AI 思考中...' : '✨ 開始你的故事'}
                    </button>
                    <button onClick={() => setStep('welcome')} className="w-full py-2 opacity-60">← 返回修改</button>
                </div>
            </div>
        );
    }
    
    // ---------- 主遊戲介面 (完全符合圖片框架) ----------
    return (
        <div className="max-w-7xl mx-auto space-y-4">
            {/* 頂部刷新按鈕 */}
            <div className="flex justify-end">
                <button onClick={refreshAll} className="px-4 py-2 rounded-full bg-white/30 backdrop-blur border flex items-center gap-2">
                    <i className="fas fa-sync-alt"></i> 刷新所有 (AI重新生成)
                </button>
            </div>
            
            {/* 三欄布局：左(人物/背包) 中(故事) 右(社交/日程/購物) */}
            <div className="grid grid-cols-1 lg:grid-cols-12 gap-4">
                {/* 左側：人物信息 + 背包財務 */}
                <div className="lg:col-span-3 space-y-4">
                    {/* 人物卡片 */}
                    <div className="card p-4">
                        <h3 className="font-bold flex items-center gap-2 mb-3"><i className="fas fa-user-circle"></i> {player.name} · {player.age}歲</h3>
                        <div className="space-y-2 text-sm">
                            <div className="flex justify-between"><span>❤️ 健康</span><span>{player.health}%</span></div>
                            <div className="w-full h-2 bg-gray-300/30 rounded-full"><div className="h-full rounded-full" style={{ width: player.health+'%', background: 'var(--accent)' }}></div></div>
                            <div className="flex justify-between"><span>⚡ 精力</span><span>{player.energy}%</span></div>
                            <div className="w-full h-2 bg-gray-300/30 rounded-full"><div className="h-full rounded-full" style={{ width: player.energy+'%', background: 'var(--accent)' }}></div></div>
                            <div className="flex justify-between"><span>😊 心情</span><span>{player.mood}%</span></div>
                            <div className="w-full h-2 bg-gray-300/30 rounded-full"><div className="h-full rounded-full" style={{ width: player.mood+'%', background: 'var(--accent)' }}></div></div>
                            <div className="flex justify-between"><span>🧠 智力</span><span>{player.iq}%</span></div>
                            <div className="w-full h-2 bg-gray-300/30 rounded-full"><div className="h-full rounded-full" style={{ width: player.iq+'%', background: 'var(--accent)' }}></div></div>
                            <div className="flex justify-between"><span>💃 魅力</span><span>{player.charm}%</span></div>
                            <div className="w-full h-2 bg-gray-300/30 rounded-full"><div className="h-full rounded-full" style={{ width: player.charm+'%', background: 'var(--accent)' }}></div></div>
                        </div>
                        <div className="mt-3 flex justify-between text-lg">
                            <span><i className="fas fa-coins"></i> ${player.money}</span>
                            <span><i className="fas fa-map-marker-alt"></i> {player.location}</span>
                        </div>
                    </div>
                    
                    {/* 背包財務 */}
                    <div className="card p-4">
                        <h3 className="font-bold flex items-center gap-2 mb-2"><i className="fas fa-backpack"></i> 背包</h3>
                        <div className="grid grid-cols-2 gap-2">
                            {inventory.map(item => (
                                <div key={item.id} className="bg-white/30 p-2 rounded flex items-center gap-2">
                                    <span>{item.icon}</span> {item.name}
                                </div>
                            ))}
                        </div>
                    </div>
                </div>
                
                {/* 中間：故事區域 (完全符合圖片) */}
                <div className="lg:col-span-6 space-y-4">
                    <div className="card p-5 h-[400px] overflow-y-auto flex flex-col space-y-3" id="story-panel">
                        {story.length === 0 ? (
                            <div className="h-full flex items-center justify-center opacity-50">
                                <p>故事即將開始...</p>
                            </div>
                        ) : (
                            story.map((msg, idx) => (
                                <div key={idx} className={`flex ${msg.role === 'user' ? 'justify-end' : 'justify-start'}`}>
                                    <div className={`max-w-[80%] p-3 ${msg.role === 'user' ? 'bubble-user' : 'bubble-assistant'}`}>
                                        {msg.content}
                                    </div>
                                </div>
                            ))
                        )}
                        {isAiThinking && <div className="text-center opacity-50">AI 正在書寫...</div>}
                    </div>
                    
                    {/* 選項區域 (三選一 + 自由輸入) */}
                    <div className="card p-4 space-y-3">
                        <div className="grid grid-cols-3 gap-2">
                            {choices.map(choice => (
                                <button
                                    key={choice.id}
                                    onClick={() => handleChoice(choice)}
                                    disabled={isAiThinking}
                                    className="p-2 rounded border hover:bg-white/30 transition text-sm"
                                    style={{ borderColor: 'var(--border)' }}
                                >
                                    <div className="font-medium">{choice.text}</div>
                                    <div className="text-xs opacity-60">{choice.hint}</div>
                                </button>
                            ))}
                        </div>
                        
                        {/* 自由輸入 (如同圖片下方的輸入欄) */}
                        <div className="flex gap-2">
                            <input
                                type="text"
                                placeholder="輸入自定義行動..."
                                className="flex-1 p-2 rounded border bg-white/30"
                                style={{ borderColor: 'var(--border)' }}
                                onKeyDown={(e) => {
                                    if (e.key === 'Enter' && !isAiThinking) {
                                        handleFreeInput(e.target.value);
                                        e.target.value = '';
                                    }
                                }}
                            />
                            <button className="p-2 px-4 rounded btn-accent" disabled={isAiThinking}>
                                <i className="fas fa-paper-plane"></i>
                            </button>
                        </div>
                    </div>
                </div>
                
                {/* 右側：社交 + 日程 + 購物 */}
                <div className="lg:col-span-3 space-y-4">
                    {/* 社交系統 (NPC 列表) */}
                    <div className="card p-4">
                        <h3 className="font-bold flex items-center gap-2 mb-2"><i className="fas fa-users"></i> 社交 · NPC</h3>
                        <div className="space-y-2 max-h-32 overflow-y-auto">
                            {npcs.length === 0 ? <p className="opacity-50 text-sm">尚無NPC</p> : npcs.map((npc, idx) => (
                                <div key={idx} className="flex items-center justify-between p-1 border-b" style={{ borderColor: 'var(--border)' }}>
                                    <span>{npc.name} <span className="text-xs opacity-60">({npc.relationship})</span></span>
                                    <button className="text-xs px-2 py-1 rounded bg-white/30" onClick={() => setChatNpc(npc)}>
                                        聊天
                                    </button>
                                </div>
                            ))}
                        </div>
                        {chatNpc && (
                            <div className="mt-2 p-2 bg-white/30 rounded">
                                <p className="font-medium">與 {chatNpc.name} 對話</p>
                                <input type="text" placeholder="輸入訊息..." className="w-full mt-1 p-1 text-sm rounded border bg-white/30" />
                            </div>
                        )}
                    </div>
                    
                    {/* 日程安排系統 */}
                    <div className="card p-4">
                        <h3 className="font-bold flex items-center gap-2 mb-2"><i className="fas fa-calendar-alt"></i> 今日日程</h3>
                        <div className="space-y-1 text-sm">
                            {schedule.length === 0 ? <p className="opacity-50">尚無安排</p> : schedule.map((item, idx) => (
                                <div key={idx} className="flex justify-between border-b py-1" style={{ borderColor: 'var(--border)' }}>
                                    <span>{item.time}</span>
                                    <span>{item.event}</span>
                                </div>
                            ))}
                        </div>
                    </div>
                    
                    {/* 購物系統 */}
                    <div className="card p-4">
                        <h3 className="font-bold flex items-center gap-2 mb-2"><i className="fas fa-store"></i> 商店</h3>
                        <div className="space-y-2">
                            {shopItems.length === 0 ? <p className="opacity-50">暫無商品</p> : shopItems.map(item => (
                                <div key={item.id} className="flex items-center justify-between text-sm">
                                    <span>{item.name} <span className="opacity-60">({item.effect})</span></span>
                                    <span>${item.price}</span>
                                </div>
                            ))}
                        </div>
                    </div>
                </div>
            </div>
            
            {/* 底部提示：所有交互都會影響AI */}
            <div className="text-center text-xs opacity-40 mt-2">
                ⚡ 點擊任何選項、輸入、聊天、購物都會觸發 AI 更新世界
            </div>
        </div>
    );
}

// ---------- 渲染到 DOM ----------
ReactDOM.createRoot(document.getElementById('app')).render(<App />);
</script>

<!-- 初始化圖標 -->
<script>
    lucide.createIcons();
</script>
</body>
</html>
