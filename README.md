import { useState, useEffect } from 'react';
import { motion, AnimatePresence } from 'motion/react';
import { 
  Home, 
  BookOpen, 
  Activity, 
  TrendingUp, 
  Settings, 
  CheckCircle2, 
  AlertCircle
} from 'lucide-react';

// --- البيانات والمحتوى ---
type Madhhab = 'shia' | 'sunni' | null;
type Page = 'home' | 'adhkar' | 'activities' | 'roadmap';

interface ContentData {
  morning: string;
  evening: string;
  dua: string;
  hadith: string;
}

const contentByMadhhab: Record<'shia' | 'sunni', ContentData> = {
  sunni: {
    morning: 'أصبحنا وأصبح الملك لله، والحمد لله، لا إله إلا الله وحده لا شريك له، له الملك وله الحمد وهو على كل شيء قدير.',
    evening: 'أمسينا وأمسى الملك لله، والحمد لله، لا إله إلا الله وحده لا شريك له، له الملك وله الحمد وهو على كل شيء قدير.',
    dua: 'اللهم أعنّي على ذكرك وشكرك وحسن عبادتك.',
    hadith: 'قال رسول الله ﷺ: أحب الأعمال إلى الله أدومها وإن قل.'
  },
  shia: {
    morning: 'اللهم إني أصبحت أشهدك وأشهد حملة عرشك وملائكتك وجميع خلقك أنك أنت الله لا إله إلا أنت.',
    evening: 'اللهم إني أمسيت في ذمتك وجوارك وأمانك، فألبسني عافيتك وسترك ورحمتك.',
    dua: 'اللهم صلّ على محمد وآل محمد، وافتح لي أبواب رحمتك، ويسّر لي أبواب رزقك.',
    hadith: 'قال الإمام علي عليه السلام: ذكرُ اللهِ دِعامةُ الإيمانِ وعِصمةٌ من الشيطان.'
  }
};

const prayers = [
  { name: 'الفجر', time: '04:12' },
  { name: 'الشروق', time: '05:39' },
  { name: 'الظهر', time: '11:49' },
  { name: 'العصر', time: '15:22' },
  { name: 'المغرب', time: '17:58' },
  { name: 'العشاء', time: '19:18' }
];

export default function App() {
  const [madhhab, setMadhhab] = useState<Madhhab>(null);
  const [page, setPage] = useState<Page>('home');
  const [fatiha, setFatiha] = useState<number>(0);
  const [tasbeeh, setTasbeeh] = useState<number>(0);
  const [tasbeehLabel, setTasbeehLabel] = useState<string>('سبحان الله');
  const [adhkarType, setAdhkarType] = useState<keyof ContentData>('morning');
  const [showOnboarding, setShowOnboarding] = useState(false);

  useEffect(() => {
    const savedMadhhab = localStorage.getItem('thaqalayn_madhhab') as Madhhab;
    const savedFatiha = localStorage.getItem('thaqalayn_fatiha');
    const savedTasbeeh = localStorage.getItem('thaqalayn_tasbeeh');
    const savedTasbeehLabel = localStorage.getItem('thaqalayn_tasbeeh_label');

    if (savedMadhhab) setMadhhab(savedMadhhab);
    else setShowOnboarding(true);

    if (savedFatiha) setFatiha(parseInt(savedFatiha));
    if (savedTasbeeh) setTasbeeh(parseInt(savedTasbeeh));
    if (savedTasbeehLabel) setTasbeehLabel(savedTasbeehLabel);
  }, []);

  const handleSetMadhhab = (type: 'shia' | 'sunni') => {
    setMadhhab(type);
    localStorage.setItem('thaqalayn_madhhab', type);
    setShowOnboarding(false);
  };

  const handleIncrementFatiha = () => {
    const newVal = fatiha + 1;
    setFatiha(newVal);
    localStorage.setItem('thaqalayn_fatiha', newVal.toString());
    if (window.navigator.vibrate) window.navigator.vibrate(12);
  };

  const handleIncrementTasbeeh = () => {
    const newVal = tasbeeh + 1;
    setTasbeeh(newVal);
    localStorage.setItem('thaqalayn_tasbeeh', newVal.toString());
    if (window.navigator.vibrate) window.navigator.vibrate(10);
  };

  return (
    <div className="relative pb-24 max-w-[880px] mx-auto min-h-screen">
      {/* الخلفية والإضاءة */}
      <div className="glow -top-64 -right-64 w-[600px] h-[600px] opacity-40" />
      <div className="glow -bottom-64 -left-64 w-[600px] h-[600px] opacity-40" />

      {/* نافذة اختيار المذهب */}
      <AnimatePresence>
        {showOnboarding && (
          <motion.div 
            initial={{ opacity: 0 }} animate={{ opacity: 1 }} exit={{ opacity: 0 }}
            className="fixed inset-0 z-[100] flex items-center justify-center p-6 backdrop-blur-2xl bg-black/80"
          >
            <motion.div 
              initial={{ scale: 0.9, y: 20 }} animate={{ scale: 1, y: 0 }}
              className="glass max-w-xl w-full p-10 flex flex-col items-center border-brand-gold/30"
            >
              <h2 className="quranic-font text-5xl gold-gradient mb-4">الثقلين</h2>
              <p className="text-brand-muted text-center mb-10 opacity-80 uppercase tracking-widest">يرجى اختيار المذهب</p>
              
              <div className="w-full flex flex-col gap-4">
                <button onClick={() => handleSetMadhhab('shia')} className="sect-card p-8 rounded-[32px] text-3xl font-bold text-[#fff4c5]">المذهب الشيعي</button>
                <button onClick={() => handleSetMadhhab('sunni')} className="sect-card p-8 rounded-[32px] text-3xl font-bold text-[#fff4c5]">المذهب السني</button>
              </div>

              <div className="mt-8 bg-white/5 border border-white/10 rounded-2xl p-6 backdrop-blur-md w-full">
                <p className="quranic-font text-center text-[#f3f0e3]">
                  بلاغ عن خطأ: التيك توك <span className="text-brand-gold">althaqalayn_</span> أو انستقرام <span className="text-brand-gold">irq_of</span>
                </p>
              </div>
            </motion.div>
          </motion.div>
        )}
      </AnimatePresence>

      {/* رأس الصفحة */}
      <header className="sticky top-0 z-50 backdrop-blur-xl border-b border-brand-line bg-brand-bg/80">
        <div className="p-6 flex items-center justify-between">
          <div className="flex items-center gap-4">
            <div className="w-12 h-12 rounded-2xl gold-gradient flex items-center justify-center shadow-lg"><span className="text-brand-bg text-2xl font-bold">☪</span></div>
            <h1 className="quranic-font text-3xl gold-gradient">الثقلين</h1>
          </div>
          <div className="px-4 py-2 rounded-full border border-brand-line bg-brand-gold/10 text-brand-gold text-xs font-bold uppercase tracking-widest">
            {madhhab === 'shia' ? 'شيعي' : madhhab === 'sunni' ? 'سني' : 'لم يحدد'}
          </div>
        </div>
      </header>

      {/* الأقسام: الرئيسية، الأذكار، الفعاليات... */}
      <main className="p-6 space-y-8">
        {page === 'home' && (
           <div className="glass p-10 relative overflow-hidden group">
             <h2 className="quranic-font text-5xl gold-gradient mb-4">رفيقك اليومي للسكينة</h2>
             <p className="text-brand-muted leading-relaxed mb-10 opacity-90">مواقيت صلاة، أذكار، وسبحة ذكية بتصميم مريح.</p>
             <div className="grid grid-cols-3 gap-4">
               <div className="stat-box"><b>128</b><span>ذكر</span></div>
               <div className="stat-box"><b>{fatiha}</b><span>فاتحة</span></div>
               <div className="stat-box"><b>{tasbeeh}</b><span>تسبيح</span></div>
             </div>
           </div>
        )}
        {/* ... بقية الأقسام ... */}
      </main>

      {/* التنقل السفلي */}
      <nav className="fixed bottom-6 left-1/2 -translate-x-1/2 w-[90%] max-w-md glass p-3 flex justify-around border-brand-line shadow-2xl z-50">
        <button onClick={() => setPage('home')} className={`nav-btn ${page === 'home' ? 'active' : ''}`}><Home /><span>الرئيسية</span></button>
        <button onClick={() => setPage('adhkar')} className={`nav-btn ${page === 'adhkar' ? 'active' : ''}`}><BookOpen /><span>الأذكار</span></button>
        <button onClick={() => setPage('activities')} className={`nav-btn ${page === 'activities' ? 'active' : ''}`}><Activity /><span>الفعاليات</span></button>
        <button onClick={() => setShowOnboarding(true)} className="nav-btn text-brand-muted"><Settings /><span>المذهب</span></button>
      </nav>
    </div>
  );
}
