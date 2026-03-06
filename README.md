<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>LOCKET BU</title>
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Hiệu ứng Emoji bay lên */
        @keyframes flyUp {
            0% { transform: translateY(0) scale(0.5); opacity: 1; }
            100% { transform: translateY(-70vh) translateX(var(--tx)) rotate(var(--tr)); opacity: 0; scale: 2; }
        }
        .emoji-fly {
            position: absolute;
            bottom: 15%;
            font-size: 4rem;
            pointer-events: none;
            animation: flyUp 2s ease-out forwards;
            z-index: 100;
        }
        /* Bo góc kiểu Locket (Squircle) */
        .locket-frame { border-radius: 60px; }
        body { background-color: black; margin: 0; overflow: hidden; }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect, useRef } = React;

        const LocketBuApp = () => {
            const [emojis, setEmojis] = useState([]);
            const [hasCamera, setHasCamera] = useState(false);
            const videoRef = useRef(null);

            // Tự động mở camera khi vào web
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
                        console.error("Lỗi camera:", err);
                    }
                }
                startCamera();
            }, []);

            // Hàm thả emoji bay khi bấm nút chụp
            const handleCapture = () => {
                const list = ['❤️', '🔥', '😂', '😍', '👍', '✨', '💀', '💩'];
                const id = Date.now();
                const newEmoji = {
                    id,
                    char: list[Math.floor(Math.random() * list.length)],
                    tx: (Math.random() - 0.5) * 300 + 'px', // Bay sang trái/phải ngẫu nhiên
                    tr: (Math.random() * 90 - 45) + 'deg'   // Xoay ngẫu nhiên
                };
                
                setEmojis(prev => [...prev, newEmoji]);
                
                // Dọn dẹp emoji sau 2 giây để nhẹ máy
                setTimeout(() => {
                    setEmojis(prev => prev.filter(e => e.id !== id));
                }, 2000);
            };

            return (
                <div className="fixed inset-0 bg-gradient-to-b from-[#3a0210] via-[#1a0106] to-black text-white flex flex-col items-center font-sans select-none">
                    
                    {/* Header: Giống hệt ảnh bạn gửi */}
                    <header className="w-full max-w-md p-6 flex justify-between items-center z-50">
                        <div className="w-10 h-10 rounded-full border-2 border-white/20 overflow-hidden shadow-lg">
                            <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Felix" alt="avatar" />
                        </div>
                        <div className="bg-white/10 backdrop-blur-xl px-5 py-2 rounded-full flex items-center gap-2 text-[13px] font-bold border border-white/10">
                            <span className="opacity-70">👥</span> 63 người bạn
                        </div>
                        <div className="w-10 h-10 bg-white/10 rounded-full flex items-center justify-center text-xl backdrop-blur-xl">💬</div>
                    </header>

                    {/* Khung Camera chính */}
                    <main className="flex-1 w-full max-w-md flex flex-col items-center justify-center px-6 relative">
                        <h2 className="absolute top-2 text-white/30 font-black tracking-[0.4em] text-[10px] uppercase">
                            LOCKET BU
                        </h2>
                        
                        <div className="relative w-full aspect-square bg-zinc-900 locket-frame overflow-hidden border-[6px] border-black/40 shadow-2xl">
                            {hasCamera ? (
                                <video ref={videoRef} autoPlay playsInline className="w-full h-full object-cover scale-x-[-1]" />
                            ) : (
                                <div className="w-full h-full flex items-center justify-center text-zinc-600 italic text-center p-10">
                                    Đang kết nối camera...
                                </div>
                            )}
                            
                            {/* Overlay icon */}
                            <div className="absolute top-6 left-6 text-white/60 text-xl">⚡</div>
                            <div className="absolute top-6 right-8 text-white/60 font-bold text-xs border border-white/20 px-1 rounded">1x</div>
                        </div>

                        {/* Vùng hiển thị Emoji bay */}
                        {emojis.map(e => (
                            <div key={e.id} className="emoji-fly" style={{ '--tx': e.tx, '--tr': e.tr }}>
                                {e.char}
                            </div>
                        ))}
                    </main>

                    {/* Bộ điều khiển bên dưới */}
                    <footer className="w-full max-w-md p-8 pb-12 flex flex-col items-center gap-10">
                        <div className="flex w-full justify-between items-center px-4">
                            <button className="p-4 bg-white/5 rounded-[28px] text-2xl active:scale-90 transition">🖼️</button>
                            
                            {/* Nút Chụp chính */}
                            <button onClick={handleCapture} className="relative w-24 h-24 flex items-center justify-center active:scale-95 transition-transform">
                                <div className="absolute inset-0 bg-gradient-to-tr from-[#ff2d55] to-[#ffb347] rounded-full blur-[1px]" />
                                <div className="relative w-[88px] h-[88px] bg-white rounded-full p-1 shadow-2xl">
                                    <div className="w-full h-full rounded-full border-[4px] border-zinc-200 bg-white" />
                                </div>
                            </button>

                            <button className="p-4 bg-white/5 rounded-full text-2xl active:scale-90 transition">🔄</button>
                        </div>

                        {/* Mục Lịch sử */}
                        <div className="flex flex-col items-center gap-2 opacity-90 cursor-pointer active:scale-95 transition">
                            <div className="flex items-center gap-2 bg-white/10 px-4 py-2 rounded-2xl border border-white/5 shadow-lg">
                                <span className="bg-[#ff2d55] text-[11px] font-black px-1.5 py-0.5 rounded shadow-sm">16</span>
                                <span className="font-bold text-sm tracking-wide">Lịch sử</span>
                            </div>
                            <div className="text-white/20 text-xs animate-bounce">▼</div>
                        </div>
                    </footer>

                    {/* Thanh gạch iPhone */}
                    <div className="w-32 h-1.5 bg-white/10 rounded-full mb-2" />
                </div>
            );
        };

        // Render ứng dụng vào thẻ #root
        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<LocketBuApp />);
    </script>
</body>
</html>
