'use client';

import React, { useState, useRef, useEffect } from 'react';
import { 
  ShieldCheck, 
  MapPin, 
  Heart, 
  X, 
  Sparkles, 
  MessageCircle, 
  CalendarHeart, 
  Camera, 
  CheckCircle2, 
  AlertTriangle, 
  PhoneCall, 
  Send, 
  ArrowRight,
  Flame,
  User,
  Clock,
  Briefcase,
  GraduationCap
} from 'lucide-react';

// ==========================================
// 1. DATA CONTRACTS & MOCK NCR PROFILES
// ==========================================
interface Profile {
  id: string;
  name: string;
  age: number;
  location: string;
  distanceKm: number;
  intent: string;
  bio: string;
  photo: string;
  work?: string;
  education?: string;
}

const NCR_PROFILES: Profile[] = [
  {
    id: 'u_1',
    name: 'Ananya',
    age: 24,
    location: 'South Delhi (Hauz Khas, Saket)',
    distanceKm: 2.4,
    intent: 'Coffee First',
    bio: 'Product designer in Saket. Weekend ritual: Blue Tokai pour-over and hunting for vintage books in Shahpur Jat.',
    photo: 'https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&w=800&q=80',
    work: 'Designer @ FinTech',
    education: 'NIFT Delhi'
  },
  {
    id: 'u_2',
    name: 'Kabir',
    age: 27,
    location: 'Gurugram (Cyber City, Golf Course Rd)',
    distanceKm: 4.8,
    intent: 'Long-term',
    bio: 'Trail runner in the Aravallis on weekends. Let’s skip small talk and discuss your favorite stand-up specials.',
    photo: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?auto=format&fit=crop&w=800&q=80',
    work: 'Lead PM @ Cyber Hub',
    education: 'IIT Delhi'
  },
  {
    id: 'u_3',
    name: 'Meher',
    age: 25,
    location: 'Noida (Sector 18, Expressway)',
    distanceKm: 7.2,
    intent: 'Marriage Minded',
    bio: 'Architect working on sustainable urban spaces. Appreciate Hindustani classical music and homemade filter coffee.',
    photo: 'https://images.unsplash.com/photo-1524504388940-b1c1722653e1?auto=format&fit=crop&w=800&q=80',
    work: 'Senior Architect',
    education: 'SPA Delhi'
  }
];

type AppScreen = 'LANDING' | 'VERIFY' | 'FEED' | 'INBOX' | 'CHAT';

export default function TrustMatchApp() {
  const [screen, setScreen] = useState<AppScreen>('LANDING');
  const [profiles, setProfiles] = useState<Profile[]>(NCR_PROFILES);
  const [isSubscribed, setIsSubscribed] = useState(false);
  const [showPaywall, setShowPaywall] = useState(false);
  const [showMatchModal, setShowMatchModal] = useState(false);
  const [showDateSafeModal, setShowDateSafeModal] = useState(false);
  const [activePartner, setActivePartner] = useState<Profile>(NCR_PROFILES[0]);

  // Verification State
  const videoRef = useRef<HTMLVideoElement>(null);
  const [cameraActive, setCameraActive] = useState(false);
  const [verifying, setVerifying] = useState(false);
  const [isVerified, setIsVerified] = useState(false);

  // Chat State
  const [messages, setMessages] = useState<{ id: string; sender: 'me' | 'partner'; text: string; time: string }[]>([
    { id: '1', sender: 'partner', text: 'Hey! Loved your bio. Are you based near Saket or CyberCity?', time: '5:42 PM' }
  ]);
  const [inputText, setInputText] = useState('');
  const [warningMessage, setWarningMessage] = useState<string | null>(null);

  // ------------------------------------------
  // CAMERA STREAM HANDLER
  // ------------------------------------------
  const startCamera = async () => {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'user' }, audio: false });
      if (videoRef.current) {
        videoRef.current.srcObject = stream;
        setCameraActive(true);
      }
    } catch {
      // Fallback if browser permission is blocked
      setCameraActive(true);
    }
  };

  const handleRunVerification = () => {
    setVerifying(true);
    setTimeout(() => {
      setVerifying(false);
      setIsVerified(true);
      setTimeout(() => {
        setScreen('FEED');
      }, 1000);
    }, 1800);
  };

  // ------------------------------------------
  // SWIPE & MATCH ACTION
  // ------------------------------------------
  const handleSwipe = (action: 'like' | 'pass') => {
    if (!profiles.length) return;
    const swiped = profiles[0];
    setProfiles((prev) => prev.slice(1));

    if (action === 'like') {
      setActivePartner(swiped);
      setShowMatchModal(true);
    }
  };

  // ------------------------------------------
  // ANTI-HARASSMENT CHAT FILTER
  // ------------------------------------------
  const handleSendMessage = () => {
    if (!inputText.trim()) return;

    // Simulated safety validator
    const abusiveKeywords = ['idiot', 'nude', 'hookup', 'rate you', 'phone'];
    const isAbusive = abusiveKeywords.some((word) => inputText.toLowerCase().includes(word));

    if (isAbusive) {
      setWarningMessage('Keep conversations respectful and safe. Inappropriate language is restricted.');
      return;
    }

    setWarningMessage(null);
    setMessages((prev) => [
      ...prev,
      {
        id: Date.now().toString(),
        sender: 'me',
        text: inputText,
        time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
      }
    ]);
    setInputText('');
  };

  return (
    <div className="max-w-md mx-auto min-h-screen bg-black text-zinc-100 flex flex-col justify-between font-sans relative border-x border-zinc-900 select-none">
      
      {/* ==================================================== */}
      {/* SCREEN 1: LANDING & VALUE PROPOSITION                */}
      {/* ==================================================== */}
      {screen === 'LANDING' && (
        <div className="flex-1 flex flex-col justify-between p-6">
          <header className="flex items-center justify-between">
            <span className="text-xl font-black bg-gradient-to-r from-emerald-400 to-teal-300 bg-clip-text text-transparent">
              TrustMatch
            </span>
            <span className="text-[10px] font-bold text-emerald-400 px-2 py-0.5 rounded-full bg-emerald-950/60 border border-emerald-800/40">
              Delhi NCR
            </span>
          </header>

          <div className="space-y-6 my-auto">
            <div className="inline-flex items-center gap-2 px-3 py-1 rounded-full bg-zinc-900 border border-zinc-800 text-xs text-zinc-300">
              <ShieldCheck className="w-4 h-4 text-emerald-400" />
              <span>100% Liveness Verified Humans</span>
            </div>

            <h1 className="text-4xl font-black tracking-tight leading-tight">
              Dating without fake profiles or <span className="text-emerald-400">₹1,500</span> paywalls.
            </h1>

            <p className="text-xs text-zinc-400 leading-relaxed">
              Connect with genuine singles in Gurugram, South Delhi, and Noida. Real biometric verification. Protected by Date Safe.
            </p>

            <div className="grid grid-cols-2 gap-3 pt-2">
              <div className="p-3 bg-zinc-950 border border-zinc-800 rounded-2xl">
                <div className="text-emerald-400 font-bold text-lg">₹10 / mo</div>
                <p className="text-[10px] text-zinc-500 mt-0.5">Less than a cup of chai.</p>
              </div>
              <div className="p-3 bg-zinc-950 border border-zinc-800 rounded-2xl">
                <div className="text-rose-400 font-bold text-lg">Date Safe SOS</div>
                <p className="text-[10px] text-zinc-500 mt-0.5">Instant emergency check-in.</p>
              </div>
            </div>
          </div>

          <button
            onClick={() => {
              setScreen('VERIFY');
              startCamera();
            }}
            className="w-full py-4 bg-emerald-500 hover:bg-emerald-400 text-zinc-950 font-bold rounded-2xl text-sm transition flex items-center justify-center gap-2 shadow-xl shadow-emerald-500/20 active:scale-95"
          >
            <span>Start Liveness Verification</span>
            <ArrowRight className="w-4 h-4" />
          </button>
        </div>
      )}

      {/* ==================================================== */}
      {/* SCREEN 2: BIOMETRIC LIVENESS SCAN                   */}
      {/* ==================================================== */}
      {screen === 'VERIFY' && (
        <div className="flex-1 flex flex-col justify-between p-6">
          <div className="text-center space-y-1">
            <div className="w-10 h-10 rounded-full bg-emerald-500/10 text-emerald-400 mx-auto flex items-center justify-center border border-emerald-500/20">
              <ShieldCheck className="w-5 h-5" />
            </div>
            <h2 className="text-base font-bold">Mandatory Liveness Scan</h2>
            <p className="text-xs text-zinc-400">Position your face inside the oval to eliminate bots.</p>
          </div>

          {/* Oval Viewfinder */}
          <div className="relative w-64 h-80 mx-auto rounded-full overflow-hidden border-2 border-emerald-500/50 bg-zinc-950 flex items-center justify-center">
            {isVerified ? (
              <div className="flex flex-col items-center text-emerald-400 animate-in zoom-in">
                <CheckCircle2 className="w-16 h-16 mb-2" />
                <span className="text-xs font-bold">100% Verified Human</span>
              </div>
            ) : (
              <>
                <video ref={videoRef} autoPlay playsInline muted className="w-full h-full object-cover scale-x-[-1]" />
                <div className="absolute inset-4 border border-dashed border-white/20 rounded-full pointer-events-none" />
              </>
            )}

            {verifying && (
              <div className="absolute inset-0 bg-black/80 flex flex-col items-center justify-center">
                <div className="w-8 h-8 border-2 border-emerald-400 border-t-transparent rounded-full animate-spin mb-2" />
                <span className="text-xs text-emerald-300">Comparing biometric points...</span>
              </div>
            )}
          </div>

          <button
            onClick={handleRunVerification}
            disabled={verifying || isVerified}
            className="w-full py-4 bg-emerald-500 hover:bg-emerald-400 text-zinc-950 font-bold rounded-2xl text-xs transition active:scale-95 shadow-lg shadow-emerald-500/20 disabled:opacity-50"
          >
            {verifying ? 'Scanning Biometrics...' : 'Verify My Profile'}
          </button>
        </div>
      )}

      {/* ==================================================== */}
      {/* SCREEN 3: HYPER-LOCAL DISCOVERY FEED                 */}
      {/* ==================================================== */}
      {screen === 'FEED' && (
        <div className="flex-1 flex flex-col justify-between p-4 pb-20">
          <header className="flex items-center justify-between py-2 border-b border-zinc-900">
            <span className="text-lg font-black bg-gradient-to-r from-emerald-400 to-teal-300 bg-clip-text text-transparent">
              TrustMatch
            </span>
            <button 
              onClick={() => setShowPaywall(true)}
              className="text-[10px] font-bold text-emerald-400 px-2.5 py-1 rounded-full bg-emerald-950 border border-emerald-800/40"
            >
              {isSubscribed ? '₹10 Pass Active' : 'Upgrade: ₹10/mo'}
            </button>
          </header>

          <main className="flex-1 flex items-center justify-center my-3">
            {profiles.length > 0 ? (
              <div className="relative w-full h-[520px] rounded-3xl overflow-hidden bg-zinc-950 border border-zinc-800 shadow-2xl">
                <img src={profiles[0].photo} alt={profiles[0].name} className="absolute inset-0 w-full h-full object-cover" />
                <div className="absolute inset-0 bg-gradient-to-t from-black via-black/30 to-transparent" />

                <div className="absolute top-4 left-4 inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-black/60 backdrop-blur-md border border-emerald-500/30 text-emerald-400 text-xs font-bold">
                  <ShieldCheck className="w-4 h-4" />
                  <span>Verified Human</span>
                </div>

                <div className="absolute bottom-0 inset-x-0 p-5 space-y-2">
                  <div className="flex items-baseline justify-between">
                    <h2 className="text-2xl font-black">{profiles[0].name}, {profiles[0].age}</h2>
                    <span className="text-[10px] font-bold text-emerald-400 px-2 py-0.5 rounded-full bg-emerald-950/80 border border-emerald-800/40">
                      {profiles[0].intent}
                    </span>
                  </div>

                  <div className="flex items-center gap-1.5 text-xs text-emerald-400 bg-emerald-950/40 border border-emerald-800/30 w-fit px-2 py-0.5 rounded-lg">
                    <MapPin className="w-3.5 h-3.5" />
                    <span>{profiles[0].location}</span>
                    <span className="text-zinc-500">•</span>
                    <span className="text-zinc-300">{profiles[0].distanceKm} km</span>
                  </div>

                  <p className="text-xs text-zinc-300 line-clamp-2 leading-relaxed">{profiles[0].bio}</p>
                </div>
              </div>
            ) : (
              <div className="text-center py-20 text-zinc-500 text-xs space-y-2">
                <p>No more profiles in this transit corridor.</p>
                <button onClick={() => setProfiles(NCR_PROFILES)} className="text-emerald-400 font-bold underline">
                  Reset Deck
                </button>
              </div>
            )}
          </main>

          {/* Action Dock */}
          <footer className="flex items-center justify-center gap-6 py-2">
            <button
              onClick={() => handleSwipe('pass')}
              aria-label="Pass"
              className="w-14 h-14 rounded-full bg-zinc-900 border border-zinc-800 flex items-center justify-center text-zinc-400 hover:text-rose-400 transition active:scale-95"
            >
              <X className="w-6 h-6 stroke-[2.5]" />
            </button>
            <button
              onClick={() => handleSwipe('like')}
              aria-label="Like"
              className="w-14 h-14 rounded-full bg-zinc-900 border border-zinc-800 flex items-center justify-center text-zinc-400 hover:text-emerald-400 transition active:scale-95"
            >
              <Heart className="w-6 h-6 stroke-[2.5]" />
            </button>
          </footer>
        </div>
      )}

      {/* ==================================================== */}
      {/* SCREEN 4: INBOX & ACTIVE CONVERSATIONS               */}
      {/* ==================================================== */}
      {screen === 'INBOX' && (
        <div className="flex-1 flex flex-col p-4 pb-20">
          <header className="pb-3 border-b border-zinc-900">
            <h2 className="text-lg font-black">Conversations</h2>
          </header>

          <div 
            onClick={() => setScreen('CHAT')}
            className="flex items-center gap-3 p-3 mt-3 rounded-2xl bg-zinc-900/60 border border-zinc-800/80 cursor-pointer hover:bg-zinc-900 transition"
          >
            <img src={activePartner.photo} alt={activePartner.name} className="w-12 h-12 rounded-full object-cover border border-emerald-500/40" />
            <div className="flex-1 min-w-0">
              <div className="flex items-center justify-between">
                <h4 className="text-xs font-bold">{activePartner.name}</h4>
                <span className="text-[10px] text-zinc-500">Just now</span>
              </div>
              <p className="text-xs text-zinc-400 truncate mt-0.5">{messages[messages.length - 1].text}</p>
            </div>
          </div>
        </div>
      )}

      {/* ==================================================== */}
      {/* SCREEN 5: SECURE CHAT & DATE PLANNER                 */}
      {/* ==================================================== */}
      {screen === 'CHAT' && (
        <div className="flex-1 flex flex-col justify-between p-4 pb-2">
          {/* Header */}
          <div className="flex items-center justify-between pb-3 border-b border-zinc-900">
            <div className="flex items-center gap-2">
              <img src={activePartner.photo} alt={activePartner.name} className="w-8 h-8 rounded-full object-cover" />
              <div>
                <h3 className="text-xs font-bold">{activePartner.name}</h3>
                <span className="text-[10px] text-zinc-400">{activePartner.location}</span>
              </div>
            </div>

            <button
              onClick={() => setShowDateSafeModal(true)}
              className="flex items-center gap-1.5 px-3 py-1.5 rounded-full bg-emerald-500/10 border border-emerald-500/30 text-emerald-400 text-xs font-bold hover:bg-emerald-500/20 transition"
            >
              <CalendarHeart className="w-3.5 h-3.5" />
              <span>Date Safe</span>
            </button>
          </div>

          {/* Messages */}
          <div className="flex-1 overflow-y-auto py-4 space-y-3">
            {messages.map((m) => (
              <div key={m.id} className={`flex flex-col ${m.sender === 'me' ? 'items-end' : 'items-start'}`}>
                <div className={`max-w-[78%] px-3.5 py-2.5 rounded-2xl text-xs ${
                  m.sender === 'me' ? 'bg-emerald-500 text-zinc-950 font-medium' : 'bg-zinc-900 text-zinc-100 border border-zinc-800'
                }`}>
                  {m.text}
                </div>
                <span className="text-[9px] text-zinc-500 px-1 mt-1">{m.time}</span>
              </div>
            ))}
          </div>

          {/* Anti-Harassment Warning */}
          {warningMessage && (
            <div className="p-2 mb-2 bg-rose-950/60 border border-rose-800/40 rounded-xl flex items-center gap-2 text-rose-300 text-[11px]">
              <AlertTriangle className="w-4 h-4 shrink-0" />
              <span>{warningMessage}</span>
            </div>
          )}

          {/* Input Box */}
          <div className="flex items-center gap-2 pt-2 border-t border-zinc-900">
            <input
              type="text"
              value={inputText}
              onChange={(e) => setInputText(e.target.value)}
              onKeyDown={(e) => e.key === 'Enter' && handleSendMessage()}
              placeholder="Send a respectful message..."
              className="flex-1 bg-zinc-900 border border-zinc-800 text-xs px-3.5 py-2.5 rounded-xl text-white focus:outline-none focus:border-emerald-500"
            />
            <button
              onClick={handleSendMessage}
              className="p-2.5 bg-emerald-500 text-zinc-950 rounded-xl hover:bg-emerald-400 transition"
            >
              <Send className="w-4 h-4 stroke-[2.5]" />
            </button>
          </div>
        </div>
      )}

      {/* ==================================================== */}
      {/* BOTTOM NAVIGATION                                    */}
      {/* ==================================================== */}
      {['FEED', 'INBOX'].includes(screen) && (
        <nav className="fixed bottom-0 inset-x-0 max-w-md mx-auto bg-zinc-950/90 backdrop-blur-md border-t border-zinc-900 px-8 py-2.5 flex items-center justify-between z-30">
          <button onClick={() => setScreen('FEED')} className={`flex flex-col items-center gap-1 ${screen === 'FEED' ? 'text-emerald-400' : 'text-zinc-500'}`}>
            <Flame className="w-5 h-5" />
            <span className="text-[10px]">Discover</span>
          </button>
          <button onClick={() => setScreen('INBOX')} className={`flex flex-col items-center gap-1 ${screen === 'INBOX' ? 'text-emerald-400' : 'text-zinc-500'}`}>
            <MessageCircle className="w-5 h-5" />
            <span className="text-[10px]">Matches</span>
          </button>
        </nav>
      )}

      {/* ==================================================== */}
      {/* POPUP: IT'S A MATCH                                  */}
      {/* ==================================================== */}
      {showMatchModal && (
        <div className="fixed inset-0 z-50 bg-black/85 backdrop-blur-md flex items-center justify-center p-4">
          <div className="w-full max-w-sm bg-zinc-900 border border-emerald-500/40 rounded-3xl p-6 text-center space-y-4">
            <div className="inline-flex items-center gap-1 px-3 py-1 rounded-full bg-emerald-500/10 text-emerald-400 text-xs font-bold">
              <Sparkles className="w-3.5 h-3.5" />
              <span>Mutual Trust Match</span>
            </div>

            <h2 className="text-xl font-black">You & {activePartner.name} matched!</h2>

            <div className="flex items-center justify-center -space-x-3 my-4">
              <img src="https://images.unsplash.com/photo-1535713875002-d1d0cf377fde?auto=format&fit=crop&w=200&q=80" alt="You" className="w-16 h-16 rounded-full border-2 border-emerald-500 object-cover" />
              <img src={activePartner.photo} alt={activePartner.name} className="w-16 h-16 rounded-full border-2 border-emerald-500 object-cover" />
            </div>

            <button
              onClick={() => {
                setShowMatchModal(false);
                setScreen('CHAT');
              }}
              className="w-full py-3.5 bg-emerald-500 hover:bg-emerald-400 text-zinc-950 font-bold rounded-xl text-xs transition"
            >
              Start Chat (₹0)
            </button>
          </div>
        </div>
      )}

      {/* ==================================================== */}
      {/* POPUP: DATE SAFE & EMERGENCY SOS                     */}
      {/* ==================================================== */}
      {showDateSafeModal && (
        <div className="fixed inset-0 z-50 bg-black/90 backdrop-blur-md flex items-center justify-center p-4">
          <div className="w-full max-w-sm bg-zinc-950 border border-zinc-800 rounded-3xl p-6 space-y-4">
            <div className="flex items-center justify-between pb-2 border-b border-zinc-800">
              <span className="text-xs font-bold text-emerald-400 flex items-center gap-1.5">
                <Clock className="w-4 h-4" /> Active Date Protection
              </span>
              <button onClick={() => setShowDateSafeModal(false)} className="text-zinc-400 text-xs">Close</button>
            </div>

            <div className="p-3 bg-zinc-900 border border-zinc-800 rounded-xl space-y-1">
              <div className="text-xs font-bold">Meeting {activePartner.name}</div>
              <div className="text-[11px] text-zinc-400 flex items-center gap-1">
                <MapPin className="w-3 h-3 text-emerald-400" /> Blue Tokai, Khan Market
              </div>
            </div>

            <button
              onClick={() => {
                alert('Status confirmed safe! Window extended by 45 mins.');
                setShowDateSafeModal(false);
              }}
              className="w-full py-3 bg-emerald-500 text-zinc-950 font-bold rounded-xl text-xs transition"
            >
              I'm Safe & Having a Good Time
            </button>

            <button
              onClick={() => {
                alert(`EMERGENCY SOS: Dispatching SMS alert to your trusted contact with GPS location: Blue Tokai, Khan Market.`);
                setShowDateSafeModal(false);
              }}
              className="w-full py-3 bg-rose-600/20 border border-rose-500/40 text-rose-400 font-bold rounded-xl text-xs hover:bg-rose-600/30 transition flex items-center justify-center gap-1.5"
            >
              <PhoneCall className="w-4 h-4" />
              <span>Trigger Emergency SOS (SMS Alert)</span>
            </button>
          </div>
        </div>
      )}

      {/* ==================================================== */}
      {/* POPUP: ₹10/MONTH UPI PAYWALL                         */}
      {/* ==================================================== */}
      {showPaywall && (
        <div className="fixed inset-0 z-50 bg-black/85 backdrop-blur-md flex items-center justify-center p-4">
          <div className="w-full max-w-sm bg-zinc-900 border border-emerald-500/30 rounded-3xl p-6 space-y-4">
            <div className="w-10 h-10 rounded-xl bg-emerald-500/10 border border-emerald-500/20 flex items-center justify-center text-emerald-400">
              <ShieldCheck className="w-6 h-6" />
            </div>

            <h3 className="text-lg font-bold">TrustMatch Unlimited Pass</h3>
            <p className="text-xs text-zinc-400">Zero bots, radical trust, and no ₹1,500/mo paywalls.</p>

            <div className="p-4 bg-zinc-950 border border-zinc-800 rounded-xl">
              <span className="text-2xl font-black text-emerald-400">₹10</span>
              <span className="text-xs text-zinc-400"> / month</span>
            </div>

            <button
              onClick={() => {
                setIsSubscribed(true);
                setShowPaywall(false);
                alert('TrustPass activated via UPI for ₹10! Enjoy unlimited matches.');
              }}
              className="w-full py-3.5 bg-emerald-500 text-zinc-950 font-bold rounded-xl text-xs transition"
            >
              Simulate UPI Payment (₹10)
            </button>
          </div>
        </div>
      )}

    </div>
  );
}
