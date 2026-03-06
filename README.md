import React, { useState, useRef, useEffect } from 'react';
import { motion, AnimatePresence } from 'framer-motion';
import { Camera, Image as ImageIcon, RefreshCcw, MessageCircle, Users, ChevronDown, Zap, X } from 'lucide-react';

// --- Hiệu ứng Emoji bay ---
const FlyingEmoji = ({ id, emoji, onComplete }) => {
  return (
    <motion.div
      initial={{ y: 0, x: 0, opacity: 1, scale: 0.5 }}
      animate={{ 
        y: -window.innerHeight * 0.7, // Bay lên cao 70% màn hình
        x: (Math.random() - 0.5) * 300, // Bay lệch sang trái/phải ngẫu nhiên
        opacity: 0, 
        scale: 2,
        rotate: Math.random() * 90 - 45 
      }}
      transition={{ duration: 2.5, ease: "easeOut" }}
      onAnimationComplete={() => onComplete(id)}
      className="absolute bottom-24 text-6xl pointer-events-none z-[100] select-none"
    >
      {emoji}
    </motion.div>
  );
};

const LocketBuApp = () => {
  const [emojis, setEmojis] = useState([]);
  const [hasCamera, setHasCamera] = useState(false);
  const videoRef = useRef(null);

  // Khởi động Camera ngay khi vào web
  useEffect(() => {
    async function setupCamera() {
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ 
          video: { facingMode: "user", aspectRatio: 1 } 
        });
        if (videoRef.current) {
          videoRef.current.srcObject = stream;
          setHasCamera(true);
        }
      } catch (err) {
        console.error("Lỗi camera: ", err);
        setHasCamera(false);
      }
    }
    setupCamera();
  }, []);

  // Hàm khi nhấn nút chụp (Thả emoji bay)
  const handleAction = () => {
    const list = ['❤️', '🔥', '😂', '😍', '👍', '✨', '💀', '💩'];
    const newEmoji = {
      id: Date.now(),
      char: list[Math.floor(Math.random() * list.length)]
    };
    setEmojis((prev) => [...prev, newEmoji]);
  };

  const removeEmoji = (id) => {
    setEmojis((prev) => prev.filter(e => e.id !== id));
  };

  return (
    <div className="fixed inset-0 bg-gradient-to-b from-[#4a0416] via-[#2a010a] to-black text-white flex flex-col items-center overflow-hidden font-sans">
      
      {/* 1. TOP BAR */}
      <header className="w-full max-w-md p-6 flex justify-between items-center z-10">
        <div className="w-10 h-10 rounded-full overflow-hidden border-2 border-white/20 shadow-lg">
          <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Felix" alt="avatar" />
        </div>
        
        <button className="bg-white/10 backdrop-blur-xl px-4 py-2 rounded-full flex items-center gap-2 text-sm font-semibold border border-white/10 shadow-inner">
          <Users size={16} />
          <span>63 người bạn</span>
        </button>

        <button className="p-2.5 bg-white/10 rounded-full backdrop-blur-xl border border-white/10">
          <MessageCircle size={22} fill="currentColor" className="opacity-90" />
        </button>
      </header>

      {/* 2. CAMERA AREA (LOCKET BU) */}
      <main className="flex-1 w-full max-w-md flex flex-col items-center justify-center px-4 relative">
        {/* Tên Web */}
        <h2 className="absolute top-2 text-white/40 font-black tracking-[0.3em] text-[10px] uppercase">
          LOCKET BU
        </h2>
        
        <div className="relative w-full aspect-square bg-zinc-900 rounded-[65px] overflow-hidden shadow-[0_20px_50px_rgba(0,0,0,0.5)] border-[5px] border-black/20">
          <Zap className="absolute top-6 left-6 text-white/70 z-10" size={20} />
          <span className="absolute top-6 right-6 text-white/70 font-bold text-xs z-10">1x</span>
          
          {hasCamera ? (
            <video 
              ref={videoRef} 
              autoPlay 
              playsInline 
              className="w-full h-full object-cover scale-x-[-1]" 
            />
          ) : (
            <div className="w-full h-full flex flex-col items-center justify-center bg-zinc-800 p-10 text-center">
              <Camera size={48} className="mb-4 text-zinc-600" />
              <p className="text-zinc-500 text-sm">Vui lòng cho phép quyền truy cập Camera để dùng Locket Bu</p>
            </div>
          )}
        </div>

        {/* Emoji Container */}
        <div className="absolute inset-0 pointer-events-none flex items-center justify-center">
          <AnimatePresence>
            {emojis.map(e => (
              <FlyingEmoji key={e.id} id={e.id} emoji={e.char} onComplete={removeEmoji} />
            ))}
          </AnimatePresence>
        </div>
      </main>

      {/* 3. BOTTOM CONTROLS */}
      <footer className="w-full max-w-md p-8 pb-10 flex flex-col items-center gap-10">
        
        <div className="flex w-full justify-between items-center px-4">
          <button className="p-4 bg-white/5 rounded-[22px] hover:bg-white/10 active:scale-95 transition">
            <ImageIcon size={28} className="text-white/80" />
          </button>

          {/* Shutter Button (Nút thả emoji) */}
          <button 
            onClick={handleAction}
            className="relative w-24 h-24 flex items-center justify-center group"
          >
            <div className="absolute inset-0 rounded-full bg-gradient-to-tr from-[#ff2d55] to-[#ffb347] animate-pulse blur-[2px]" />
            <div className="relative w-[88px] h-[88px] bg-white rounded-full p-1 shadow-inner active:scale-90 transition-transform">
               <div className="w-full h-full rounded-full border-[3px] border-zinc-200 bg-white" />
            </div>
          </button>

          <button className="p-4 bg-white/5 rounded-full hover:bg-white/10 active:scale-95 transition">
            <RefreshCcw size={28} className="text-white/80" />
          </button>
        </div>

        {/* 4. HISTORY SECTION */}
        <div className="flex flex-col items-center gap-2 cursor-pointer group">
          <div className="flex items-center gap-2 bg-white/5 px-4 py-2 rounded-2xl group-active:scale-95 transition">
            <span className="bg-[#ff2d55] text-white text-[10px] font-black px-1.5 py-0.5 rounded-md shadow-lg shadow-pink-500/20">16</span>
            <span className="font-bold text-sm tracking-wide text-white/90">Lịch sử</span>
          </div>
          <ChevronDown size={20} className="text-white/30 group-hover:text-white transition-colors" />
        </div>
      </footer>

      {/* iPhone Home Bar */}
      <div className="w-32 h-1.5 bg-white/10 rounded-full mb-2" />
    </div>
  );
};

export default LocketBuApp;
