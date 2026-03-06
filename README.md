<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LOCKET BU</title>
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @keyframes flyUp {
            0% { transform: translateY(0) scale(0.5); opacity: 1; }
            100% { transform: translateY(-600px) translateX(var(--tx)) rotate(var(--tr)); opacity: 0; scale: 2; }
        }
        .emoji-fly {
            position: absolute;
            bottom: 120px;
            font-size: 3.5rem;
            pointer-events: none;
            animation: flyUp 2s ease-out forwards;
            z-index: 100;
        }
        .squircle { border-radius: 65px; }
    </style>
</head>
<body class="bg-black">
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect, useRef } = React;

        const LocketBuApp = () => {
            const [emojis, setEmojis] = useState([]);
            const [hasCamera, setHasCamera] = useState(false);
            const videoRef = useRef(null);

            // Khởi động Camera
            useEffect(() => {
                async function startCamera() {
                    try {
                        const stream = await navigator.mediaDevices.getUserMedia({ 
                            video: { facingMode: "user", aspectRatio: 1 } 
                        });
                        if (videoRef.current) {
                            videoRef.current.srcObject = stream;
                            setHasCamera(true);
                        }
                    } catch (err) {
                        console.error("Không mở được camera:", err);
                    }
                }
                startCamera();
            }, []);

            // Thả Emoji bay
            const dropEmoji = () => {
                const list = ['❤️', '🔥', '😂', '😍', '👍', '✨', '💀', '💩'];
                const newEmoji = {
                    id: Date.now(),
                    char: list[Math.floor(Math.random() * list.length)],
                    tx: (Math.random() - 0.5) * 300 + 'px', // Bay lệch trái phải
                    tr: (Math.random() * 90 - 45) + 'deg'   // Xoay ngẫu nhiên
                };
                setEmojis(prev => [...prev, newEmoji]);
                
                // Tự xóa emoji sau khi bay xong để nhẹ máy
                setTimeout(() => {
                    setEmojis(prev => prev.filter(e => e.id !== newEmoji.id));
                }, 2000);
            };

            return (
                <div className="fixed inset-0 bg-gradient-to-b from-[#4a0416] via-[#2a010a] to-black text-white flex flex-col items-center overflow-hidden font-sans">
                    
                    {/* Header */}
                    <header className="w-full max-w-md p-6 flex justify-between items-center z-50">
                        <div className="w-10 h-10 rounded-full border-2 border-white/20 overflow-hidden shadow-lg">
                            <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Felix" alt="avatar" />
                        </div>
                        <div className="bg-white/10 backdrop-blur-xl px-4 py-2 rounded-full flex items-center gap-2 text-xs font-bold border border-white/10">
                            👤 63 người bạn
                        </div>
                        <div className="w-10 h-10 bg-white/10 rounded-full flex items-center justify-center text-xl">💬</div>
                    </header>

                    {/* Khung Camera LOCKET BU */}
                    <main className="flex-1 w-full max-w-md flex flex-col items-center justify-center px-6 relative">
                        <h2 className="absolute top-2 text-white/30 font-black tracking-[0.3em] text-[10px]">LOCKET BU</h2>
                        
                        <div className="relative w-full aspect-square bg-zinc-900 squircle overflow-hidden border-[5px] border-black/30 shadow-2xl">
                            {hasCamera ? (
                                <video ref={videoRef} autoPlay playsInline className="w-full h-full object-cover scale-x-[-1]" />
                            ) : (
                                <div className="w-full h-full flex items-center justify-center text-zinc-600 italic px-10 text-center">
                                    Đang chờ quyền camera...
                                </div>
                            )}
                            
                            {/* Icon Flash ảo */}
                            <div className="absolute top-6 left-6 text-white/70">⚡</div>
                            <div className="absolute top-6 right-6 text-white/70 font-bold text-xs">1x</div>
                        </div>

                        {/* Vùng Emoji bay */}
                        {emojis.map(e => (
                            <div key={e.id} className="emoji-fly" style={{ '--tx': e.tx, '--tr': e.tr }}>
                                {e.char}
                            </div>
                        ))}
                    </main>

                    {/* Nút bấm phía dưới */}
                    <footer className="w-full max-w-md p-8 pb-12 flex flex-col items-center gap-10">
                        <div className="flex w-full justify-between items-center px-4">
                            <button className="p-4 bg-white/5 rounded-[25px] text-2xl">🖼️</button>
                            
                            {/* Nút Chụp + Thả Emoji */}
                            <button onClick={dropEmoji} className="relative w-24 h-24 flex items-center justify-center active:scale-90 transition-transform">
                                <div className="absolute inset-0 bg-gradient-to-tr from-[#ff2d55] to-[#ffb347] rounded-full blur-[2px]" />
                                <div className="relative w-[86px] h-[86px] bg-white rounded-full p-1 shadow-xl">
                                    <div className="w-full h-full rounded-full border-[3px] border-zinc-200 bg-white" />
                                </div>
                            </button>

                            <button className="p-4 bg-white/5 rounded-full text-2xl">🔄</button>
                        </div>

                        {/* Lịch sử */}
                        <div className="flex flex-col items-center gap-2 opacity-80 active:scale-95 transition">
                            <div className="flex items-center gap-2 bg-white/5 px-4 py-2 rounded-2xl border border-white/5">
                                <span className="bg-[#ff2d55] text-[10px] font-black px-1.5 py-0.5 rounded">16</span>
                                <span className="font-bold text-sm tracking-wide">Lịch sử</span>
                            </div>
                            <div className="text-white/20 animate-bounce">▼</div>
                        </div>
                    </footer>
                </div>
            );
        };

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<LocketBuApp />);
    </script>
</body>
</html>
