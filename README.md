import React, { useState, useEffect, useRef } from 'react';
import { db, storage } from './firebase';
import { collection, addDoc, query, orderBy, onSnapshot } from 'firebase/firestore';
import { ref, uploadString, getDownloadURL } from 'firebase/storage';
import { Camera, RefreshCw, Send, User, X } from 'lucide-react';
import { motion, AnimatePresence } from 'framer-motion';

const LocketClone = () => {
  const [posts, setPosts] = useState([]);
  const [stream, setStream] = useState(null);
  const [capturedImg, setCapturedImg] = useState(null);
  const [loading, setLoading] = useState(false);
  const videoRef = useRef(null);

  // 1. Lấy dữ liệu bài đăng real-time
  useEffect(() => {
    const q = query(collection(db, "posts"), orderBy("createdAt", "desc"));
    const unsubscribe = onSnapshot(q, (snapshot) => {
      setPosts(snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() })));
    });
    return () => unsubscribe();
  }, []);

  // 2. Mở Camera
  const startCamera = async () => {
    const mediaStream = await navigator.mediaDevices.getUserMedia({ 
      video: { aspectWeight: 1, facingMode: "user" } 
    });
    setStream(mediaStream);
    if (videoRef.current) videoRef.current.srcObject = mediaStream;
  };

  // 3. Chụp ảnh từ Video stream
  const takePhoto = () => {
    const canvas = document.createElement('canvas');
    canvas.width = 500; canvas.height = 500;
    const ctx = canvas.getContext('2d');
    ctx.drawImage(videoRef.current, 0, 0, 500, 500);
    setCapturedImg(canvas.toDataURL('image/jpeg'));
    // Tắt camera sau khi chụp
    stream.getTracks().forEach(track => track.stop());
    setStream(null);
  };

  // 4. Gửi ảnh lên Firebase
  const handleUpload = async () => {
    if (!capturedImg) return;
    setLoading(true);
    try {
      const storageRef = ref(storage, `posts/${Date.now()}.jpg`);
      await uploadString(storageRef, capturedImg, 'data_url');
      const url = await getDownloadURL(storageRef);
      
      await addDoc(collection(db, "posts"), {
        imageUrl: url,
        user: "User_Web",
        createdAt: new Date()
      });
      setCapturedImg(null);
    } catch (e) { console.error(e); }
    setLoading(false);
  };

  return (
    <div className="min-h-screen bg-black text-white flex flex-col items-center overflow-x-hidden">
      {/* Header */}
      <div className="w-full max-w-md p-5 flex justify-between items-center sticky top-0 bg-black/50 backdrop-blur-lg z-50">
        <User className="text-gray-400" />
        <h1 className="text-xl font-black tracking-tighter italic">LOCKET CLONE</h1>
        <div className="w-6" />
      </div>

      {/* Feed */}
      <div className="w-full max-w-md space-y-10 p-4 pb-40">
        {posts.map((post) => (
          <motion.div initial={{ opacity: 0, y: 20 }} animate={{ opacity: 1, y: 0 }} key={post.id} className="space-y-3">
            <div className="flex items-center gap-2 px-2">
              <div className="w-7 h-7 bg-yellow-400 rounded-full" />
              <span className="font-bold text-sm">{post.user}</span>
            </div>
            <div className="aspect-square rounded-[45px] overflow-hidden border-[6px] border-zinc-900 shadow-2xl">
              <img src={post.imageUrl} className="w-full h-full object-cover" alt="Locket" />
            </div>
          </motion.div>
        ))}
      </div>

      {/* Camera / Preview Overlay */}
      <AnimatePresence>
        {(stream || capturedImg) && (
          <motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }} exit={{ opacity: 0 }} 
            className="fixed inset-0 bg-black z-[100] flex flex-col items-center justify-center p-6">
            <button onClick={() => { setStream(null); setCapturedImg(null); }} className="absolute top-10 right-10 p-2 bg-zinc-800 rounded-full">
              <X />
            </button>
            
            <div className="w-full max-w-sm aspect-square rounded-[50px] overflow-hidden border-4 border-yellow-400">
              {stream && <video ref={videoRef} autoPlay playsInline className="w-full h-full object-cover scale-x-[-1]" />}
              {capturedImg && <img src={capturedImg} className="w-full h-full object-cover" alt="preview" />}
            </div>

            <div className="mt-10">
              {stream ? (
                <button onClick={takePhoto} className="w-20 h-20 bg-white rounded-full border-8 border-gray-300" />
              ) : (
                <button onClick={handleUpload} disabled={loading} className="flex items-center gap-2 bg-yellow-400 text-black px-8 py-4 rounded-full font-bold">
                  {loading ? <RefreshCw className="animate-spin" /> : <Send />}
                  {loading ? "Đang gửi..." : "Gửi ngay"}
                </button>
              )}
            </div>
          </motion.div>
        )}
      </AnimatePresence>

      {/* Bottom Nav */}
      {!stream && !capturedImg && (
        <div className="fixed bottom-8 w-full max-w-xs flex justify-center">
          <button onClick={startCamera} className="w-20 h-20 bg-yellow-400 rounded-full flex items-center justify-center shadow-[0_0_30px_rgba(250,204,21,0.5)] active:scale-90 transition">
            <Camera size={35} color="black" strokeWidth={2.5} />
          </button>
        </div>
      )}
    </div>
  );
};

export default LocketClone;
