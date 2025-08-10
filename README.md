# import React, { useState, useEffect } from 'react';
import { Aperture, Clipboard, Check, Sparkles, LoaderCircle, Palette, Wand2, Image as ImageIcon, MessageSquareQuote, Layers, Lightbulb, Volume2, Send, Type, Building2, Utensils, Cpu, Mountain, Store, BrainCircuit, Paintbrush } from 'lucide-react';

// Main App Component
export default function App() {
  // State for user inputs
  const [businessDescription, setBusinessDescription] = useState('Kedai kopi modern di Tegal yang menyajikan kopi lokal dengan suasana yang nyaman untuk anak muda.');
  const [subject, setSubject] = useState('Kopi Tegal');
  const [slogan, setSlogan] = useState('');
  const [style, setStyle] = useState('umkm');
  const [font, setFont] = useState('sans-serif');
  const [elements, setElements] = useState('cangkir kopi, poci teh tanah liat');
  const [palette, setPalette] = useState('coklat hangat dan krem');
  const [background, setBackground] = useState('white');
  const [details, setDetails] = useState({
    vector: true, flat: true, '3d_render': false, mascot: false, emblem: false,
    'line_art': false, 'negative_space': false, gradient: false, 'pixel_art': false,
    '4k': false, 'logo_design': true,
  });

  // State for generated content
  const [suggestedNames, setSuggestedNames] = useState([]);
  const [generatedPrompt, setGeneratedPrompt] = useState('');
  const [generatedImage, setGeneratedImage] = useState(null);
  const [logoFeedback, setLogoFeedback] = useState('');
  const [socialPost, setSocialPost] = useState('');

  // State for UI and loading
  const [isSuggestingNames, setIsSuggestingNames] = useState(false);
  const [isSuggestingDetails, setIsSuggestingDetails] = useState(false);
  const [isCopied, setIsCopied] = useState(false);
  const [isSuggestingElements, setIsSuggestingElements] = useState(false);
  const [isSuggestingPalette, setIsSuggestingPalette] = useState(false);
  const [isGeneratingMagic, setIsGeneratingMagic] = useState(false);
  const [isGeneratingImage, setIsGeneratingImage] = useState(false);
  const [isGeneratingSlogan, setIsGeneratingSlogan] = useState(false);
  const [isGettingFeedback, setIsGettingFeedback] = useState(false);
  const [isGeneratingPost, setIsGeneratingPost] = useState(false);
  const [isSpeaking, setIsSpeaking] = useState(false);
  const [error, setError] = useState(null);

  // Options for dropdowns
  const styleOptions = [
    { value: 'minimalist', label: 'Minimalis' }, { value: 'typography', label: 'Tipografi' },
    { value: 'modern', label: 'Modern' }, { value: 'vintage', label: 'Vintage' },
    { value: 'elegant', label: 'Elegan' }, { value: 'hand-drawn', label: 'Gambar Tangan' },
    { value: 'abstract', label: 'Abstrak' }, { value: 'geometric', label: 'Geometris' },
    { value: 'umkm', label: 'UMKM / Lokal' }, { value: 'teknologi', label: 'Teknologi / Startup' },
    { value: 'makanan', label: 'Makanan & Minuman' }, { value: 'properti', label: 'Properti / Real Estate' },
    { value: 'geografis', label: 'Geografis / Pariwisata' },
  ];

  const fontOptions = [
    { value: 'serif', label: 'Serif (Klasik, Elegan)' }, { value: 'sans-serif', label: 'Sans-serif (Modern, Bersih)' },
    { value: 'script', label: 'Script (Tulisan Tangan)' }, { value: 'display', label: 'Display (Unik, Dekoratif)' },
    { value: 'blackletter', label: 'Blackletter (Gothic)' },
  ];

  const backgroundOptions = [
    { value: 'white', label: 'Putih Polos' }, { value: 'black', label: 'Hitam Polos' },
    { value: 'transparent', label: 'Transparan' }, { value: 'light gray', label: 'Abu-abu Terang' },
    { value: 'dark wood texture', label: 'Tekstur Kayu Gelap' }, { value: 'concrete wall', label: 'Dinding Beton' },
    { value: 'blurry office background', label: 'Latar Kantor Buram' },
  ];

  // Effect to regenerate prompt
  useEffect(() => {
    const detailParts = Object.entries(details).filter(([_, v]) => v).map(([k]) => k.replace(/_/g, ' '));
    const bgText = background === 'transparent' ? 'dengan latar belakang transparan' : `dengan latar belakang ${background}`;
    const fontText = style === 'typography' ? `menggunakan font ${font}` : '';
    let industryContext = '';
    switch (style) {
      case 'umkm': industryContext = 'logo untuk usaha kecil menengah (UMKM) yang ramah dan mudah diingat'; break;
      case 'teknologi': industryContext = 'logo untuk perusahaan teknologi atau startup yang inovatif dan modern'; break;
      case 'makanan': industryContext = 'logo untuk brand makanan atau minuman yang lezat dan menggugah selera'; break;
      case 'properti': industryContext = 'logo untuk perusahaan properti atau real estate yang mewah dan terpercaya'; break;
      case 'geografis': industryContext = 'logo yang merepresentasikan lokasi geografis atau destinasi pariwisata'; break;
      default: industryContext = `dengan gaya ${style}`;
    }
    const promptParts = [
      industryContext, `untuk brand bernama ${subject}`, slogan ? `dengan slogan "${slogan}"` : '',
      `elemen visual utama: ${elements}`, fontText, `palet warna ${palette}`, bgText, ...detailParts
    ];
    setGeneratedPrompt(promptParts.filter(p => p && String(p).trim()).join(', '));
  }, [subject, slogan, style, font, elements, palette, background, details]);

  // Handlers
  const handleDetailChange = (e) => setDetails(p => ({ ...p, [e.target.name]: e.target.checked }));
  const handleError = (message) => { setError(message); setTimeout(() => setError(null), 3000); };

  // TTS Helper
  const base64ToArrayBuffer = (b) => { const s = window.atob(b); const l = s.length; const a = new Uint8Array(l); for (let i = 0; i < l; i++)a[i] = s.charCodeAt(i); return a.buffer; };
  const pcmToWav = (d, r) => { const b = new ArrayBuffer(44 + d.length * 2); const v = new DataView(b); v.setUint32(0, 0x52494646, false); v.setUint32(4, 36 + d.length * 2, true); v.setUint32(8, 0x57415645, false); v.setUint32(12, 0x666d7420, false); v.setUint32(16, 16, true); v.setUint16(20, 1, true); v.setUint16(22, 1, true); v.setUint32(24, r, true); v.setUint32(28, r * 2, true); v.setUint16(32, 2, true); v.setUint16(34, 16, true); v.setUint32(36, 0x64617461, false); v.setUint32(40, d.length * 2, true); for (let i = 0; i < d.length; i++) { v.setInt16(44 + i * 2, d[i], true); } return new Blob([v], { type: 'audio/wav' }); };

  // Gemini API calls
  const callGeminiForText = async (prompt) => {
    const payload = { contents: [{ role: "user", parts: [{ text: prompt }] }] };
    const apiKey = "";
    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
    const response = await fetch(apiUrl, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(payload) });
    if (!response.ok) throw new Error(`Permintaan API gagal: ${response.statusText}`);
    const result = await response.json();
    if (result.candidates?.[0]?.content?.parts?.[0]?.text) return result.candidates[0].content.parts[0].text.trim().replace(/"/g, '');
    throw new Error("Respons API tidak valid.");
  };

  const handleSuggestNames = async () => {
    if (!businessDescription) return handleError("Silakan deskripsikan bisnis Anda terlebih dahulu.");
    setIsSuggestingNames(true); setError(null); setSuggestedNames([]);
    const prompt = `Anda adalah seorang ahli branding. Berdasarkan deskripsi bisnis ini: "${businessDescription}", sarankan 5 nama brand yang unik, singkat, dan mudah diingat. Jawab hanya dengan daftar yang dipisahkan koma, tanpa penomoran atau kata pengantar. Contoh: Kopi Senja,Aroma Pagi,Sudut Tegal,Cerita Kopi,Teguk Bahagia`;
    try {
      const namesString = await callGeminiForText(prompt);
      setSuggestedNames(namesString.split(',').map(name => name.trim()));
    } catch (err) {
      console.error(err); handleError("Gagal mendapatkan saran nama.");
    } finally {
      setIsSuggestingNames(false);
    }
  };

  const handleSuggestDetails = async () => {
    if (!subject) return handleError("Silakan masukkan subjek untuk mendapatkan saran detail.");
    setIsSuggestingDetails(true); setError(null);
    const prompt = `Anda adalah konsultan desain. Untuk sebuah logo brand bernama "${subject}" dengan deskripsi "${businessDescription}", gaya yang dipilih adalah "${style}". Sarankan 2-4 detail teknis atau gaya tambahan yang paling cocok dari daftar berikut: vector, flat, 3d_render, mascot, emblem, line_art, negative_space, gradient, pixel_art. Berikan respons Anda sebagai objek JSON dengan satu kunci "suggestions" yang berisi array string dari detail yang disarankan. Contoh: {"suggestions": ["vector", "emblem"]}`;
    const payload = {
      contents: [{ role: "user", parts: [{ text: prompt }] }],
      generationConfig: {
        responseMimeType: "application/json",
        responseSchema: {
          type: "OBJECT",
          properties: {
            suggestions: {
              type: "ARRAY",
              items: { type: "STRING" }
            }
          },
          required: ["suggestions"]
        }
      }
    };
    const apiKey = "";
    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
    try {
      const response = await fetch(apiUrl, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(payload) });
      if (!response.ok) throw new Error(`Permintaan API gagal: ${response.statusText}`);
      const result = await response.json();
      if (result.candidates?.[0]) {
        const data = JSON.parse(result.candidates[0].content.parts[0].text);
        if (data.suggestions && Array.isArray(data.suggestions)) {
          const newDetails = { ...details };
          // Reset some conflicting details first for a cleaner slate
          Object.keys(newDetails).forEach(k => {
            if (['vector', 'flat', '3d_render', 'mascot', 'emblem', 'line_art', 'pixel_art'].includes(k)) {
              newDetails[k] = false;
            }
          });
          data.suggestions.forEach(suggestion => {
            if (newDetails.hasOwnProperty(suggestion)) {
              newDetails[suggestion] = true;
            }
          });
          setDetails(newDetails);
        }
      } else { throw new Error("Respons API tidak valid."); }
    } catch (err) {
      console.error(err); handleError("Gagal mendapatkan saran detail.");
    } finally {
      setIsSuggestingDetails(false);
    }
  };

  const handleGenerateSlogan = async () => {
    if (!subject) return handleError("Silakan masukkan subjek untuk membuat slogan.");
    setIsGeneratingSlogan(true); setError(null);
    try { setSlogan(await callGeminiForText(`Buatkan satu slogan atau tagline yang singkat, menarik, dan profesional untuk sebuah brand bernama "${subject}". Jawab hanya dengan teks slogan saja, tanpa tanda kutip.`)); }
    catch (err) { console.error(err); handleError("Gagal membuat slogan."); }
    finally { setIsGeneratingSlogan(false); }
  };

  const handleSuggestElements = async () => {
    if (!subject) return handleError("Silakan masukkan subjek terlebih dahulu.");
    setIsSuggestingElements(true); setError(null);
    try { setElements(await callGeminiForText(`Berikan 5 hingga 7 ide elemen visual yang relevan untuk desain logo tentang "${subject}" dengan gaya "${style}". Jawab hanya dengan daftar yang dipisahkan koma.`)); }
    catch (err) { console.error(err); handleError("Gagal mendapatkan saran elemen."); }
    finally { setIsSuggestingElements(false); }
  };

  const handleSuggestPalette = async () => {
    if (!subject || !style) return handleError("Masukkan subjek dan gaya untuk saran warna terbaik.");
    setIsSuggestingPalette(true); setError(null);
    try { setPalette(await callGeminiForText(`Sarankan satu palet warna yang menarik untuk desain logo dengan subjek "${subject}" dan gaya "${style}". Jawab hanya dengan deskripsi singkat palet warna dalam bahasa Indonesia.`)); }
    catch (err) { console.error(err); handleError("Gagal mendapatkan saran palet."); }
    finally { setIsSuggestingPalette(false); }
  };

  const handleGenerateMagicPrompt = async () => {
    if (!subject) return handleError("Silakan masukkan subjek untuk memulai keajaiban.");
    setIsGeneratingMagic(true); setError(null);
    const prompt = `Anda adalah konsultan desain logo ahli. Untuk merek "${subject}" dengan gaya "${style}", buat konsep prompt logo. Beri respons sebagai objek JSON dengan kunci "elements", "palette", dan "font" (jika style adalah tipografi).`;
    const payload = { contents: [{ role: "user", parts: [{ text: prompt }] }], generationConfig: { responseMimeType: "application/json", responseSchema: { type: "OBJECT", properties: { elements: { type: "STRING" }, palette: { type: "STRING" }, font: { type: "STRING" } }, required: ["elements", "palette"] } } };
    const apiKey = "";
    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
    try {
      const response = await fetch(apiUrl, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(payload) });
      if (!response.ok) throw new Error(`Permintaan API gagal: ${response.statusText}`);
      const result = await response.json();
      if (result.candidates?.[0]) {
        const data = JSON.parse(result.candidates[0].content.parts[0].text);
        setElements(data.elements); setPalette(data.palette);
        if (style === 'typography' && data.font) { setFont(data.font); }
      } else { throw new Error("Respons API tidak valid."); }
    } catch (err) { console.error(err); handleError("Gagal membuat prompt ajaib."); }
    finally { setIsGeneratingMagic(false); }
  };

  const handleGenerateImage = async () => {
    if (!generatedPrompt) return handleError("Prompt tidak boleh kosong.");
    setIsGeneratingImage(true); setError(null); setGeneratedImage(null); setLogoFeedback(''); setSocialPost('');
    const payload = { instances: [{ prompt: generatedPrompt }], parameters: { "sampleCount": 1 } };
    const apiKey = "";
    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-002:predict?key=${apiKey}`;
    try {
      const response = await fetch(apiUrl, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(payload) });
      if (!response.ok) throw new Error(`Permintaan API gambar gagal: ${response.statusText}`);
      const result = await response.json();
      if (result.predictions?.[0]?.bytesBase64Encoded) setGeneratedImage(`data:image/png;base64,${result.predictions[0].bytesBase64Encoded}`);
      else throw new Error("Data gambar tidak ditemukan.");
    } catch (err) { console.error(err); handleError("Gagal membuat gambar logo."); }
    finally { setIsGeneratingImage(false); }
  };

  const handleGetFeedback = async () => {
    if (!generatedImage) return handleError("Buat logo terlebih dahulu untuk dianalisis.");
    setIsGettingFeedback(true); setError(null); setLogoFeedback('');
    const prompt = `Anda adalah seorang desainer grafis profesional. Berikan masukan membangun untuk logo ini, untuk brand "${subject}". Fokus pada komposisi, warna, dan keterbacaan. Beri 2-3 poin singkat dalam format markdown. Jawab dalam bahasa Indonesia.`;
    const payload = { contents: [{ role: "user", parts: [{ text: prompt }, { inlineData: { mimeType: "image/png", data: generatedImage.split(',')[1] } }] }] };
    const apiKey = "";
    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
    try {
      const response = await fetch(apiUrl, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(payload) });
      if (!response.ok) throw new Error(`Permintaan API masukan gagal: ${response.statusText}`);
      const result = await response.json();
      if (result.candidates?.[0]?.content?.parts?.[0]?.text) setLogoFeedback(result.candidates[0].content.parts[0].text);
      else throw new Error("Respons API masukan tidak valid.");
    } catch (err) { console.error(err); handleError("Gagal mendapatkan masukan AI."); }
    finally { setIsGettingFeedback(false); }
  };

  const handleSpeakSlogan = async () => {
    if (!slogan) return handleError("Slogan kosong, tidak ada yang bisa diucapkan.");
    setIsSpeaking(true); setError(null);
    const payload = { contents: [{ parts: [{ text: `Ucapkan dengan nada profesional dan jelas: ${slogan}` }] }], generationConfig: { responseModalities: ["AUDIO"], speechConfig: { voiceConfig: { prebuiltVoiceConfig: { voiceName: "Kore" } } } }, model: "gemini-2.5-flash-preview-tts" };
    const apiKey = "";
    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-tts:generateContent?key=${apiKey}`;
    try {
      const response = await fetch(apiUrl, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(payload) });
      if (!response.ok) throw new Error(`Permintaan API TTS gagal: ${response.statusText}`);
      const result = await response.json();
      const part = result?.candidates?.[0]?.content?.parts?.[0];
      if (part?.inlineData?.data && part.inlineData.mimeType.startsWith("audio/")) {
        const sampleRate = parseInt(part.inlineData.mimeType.match(/rate=(\d+)/)[1], 10);
        const pcmData = new Int16Array(base64ToArrayBuffer(part.inlineData.data));
        const wavBlob = pcmToWav(pcmData, sampleRate);
        new Audio(URL.createObjectURL(wavBlob)).play();
      } else { throw new Error("Data audio tidak valid."); }
    } catch (err) { console.error(err); handleError("Gagal menghasilkan audio."); }
    finally { setIsSpeaking(false); }
  };

  const handleGeneratePost = async () => {
    if (!generatedImage) return handleError("Buat logo dahulu untuk membuat postingan.");
    setIsGeneratingPost(true); setError(null); setSocialPost('');
    const prompt = `Anda adalah ahli marketing media sosial. Buat satu postingan pengumuman yang menarik untuk peluncuran brand baru bernama "${subject}". Slogan mereka adalah "${slogan}". Ini adalah logo baru mereka. Buat caption yang antusias dan sertakan 3-5 tagar yang relevan. Jawab dalam bahasa Indonesia.`;
    const payload = { contents: [{ role: "user", parts: [{ text: prompt }, { inlineData: { mimeType: "image/png", data: generatedImage.split(',')[1] } }] }] };
    const apiKey = "";
    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
    try {
      const response = await fetch(apiUrl, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(payload) });
      if (!response.ok) throw new Error(`Permintaan API postingan gagal: ${response.statusText}`);
      const result = await response.json();
      if (result.candidates?.[0]?.content?.parts?.[0]?.text) setSocialPost(result.candidates[0].content.parts[0].text);
      else throw new Error("Respons API postingan tidak valid.");
    } catch (err) { console.error(err); handleError("Gagal membuat postingan."); }
    finally { setIsGeneratingPost(false); }
  };

  const handleCopy = () => {
    const textArea = document.createElement("textarea");
    textArea.value = generatedPrompt;
    document.body.appendChild(textArea);
    textArea.select();
    try {
      document.execCommand('copy');
      setIsCopied(true);
      setTimeout(() => setIsCopied(false), 2000);
    } catch (err) {
      console.error('Gagal menyalin teks: ', err);
      handleError('Gagal menyalin teks.');
    }
    document.body.removeChild(textArea);
  };

  return (
    <div className= "bg-gray-900 text-white min-h-screen font-sans flex items-center justify-center p-4" >
    <div className="w-full max-w-6xl bg-gray-800/50 backdrop-blur-sm rounded-2xl shadow-2xl overflow-hidden border border-gray-700" >
      <div className="p-8" >
        <div className="flex items-center gap-4 mb-6" > <Aperture className="w-10 h-10 text-cyan-400" /> <div><h1 className="text-2xl font-bold text-white" > Logoloka < /h1><p className="text-gray-400">Asisten Branding AI Anda</p > </div></div >
          <div className="grid md:grid-cols-2 gap-8" >
            <div className="space-y-5" >
              <div>
              <label className="block text-sm font-medium text-gray-300 mb-2" > 0. Cari Inspirasi Nama Brand(Opsional) < /label>
                < div className = "bg-gray-900/50 p-4 rounded-lg space-y-3 border border-gray-700" >
                  <textarea value={ businessDescription } onChange = {(e) => setBusinessDescription(e.target.value)
} placeholder = "Jelaskan ide bisnis Anda di sini..." className = "w-full bg-gray-700 border-gray-600 rounded-lg p-2 focus:ring-2 focus:ring-cyan-500 h-20 text-sm" />
  <button onClick={ handleSuggestNames } disabled = { isSuggestingNames } className = "w-full flex items-center justify-center gap-3 p-3 bg-teal-600 hover:bg-teal-700 rounded-lg text-white font-semibold disabled:opacity-50" > { isSuggestingNames?<LoaderCircle className = "animate-spin"/> : <BrainCircuit/>} {isSuggestingNames ? 'Mencari Ide...' : '✨ Dapatkan Ide Nama'}</button >
    { suggestedNames.length > 0 && <div className="flex flex-wrap gap-2 pt-2"> { suggestedNames.map(name => <button key={ name } onClick = {() => setSubject(name)} className="bg-gray-600 hover:bg-cyan-700 text-xs font-semibold px-3 py-1 rounded-full transition-colors" > { name } < /button>)}</div >}
    < /div>
    < /div>
    < div >
    <label className="block text-sm font-medium text-gray-300 mb-2" > 1. Definisikan Brand Anda < /label>
      < div className = "space-y-3" >
        <input type="text" value = { subject } onChange = {(e) => setSubject(e.target.value)} placeholder = "Nama Brand / Subjek Utama" className = "w-full bg-gray-700 border border-gray-600 rounded-lg p-3 focus:ring-2 focus:ring-cyan-500" />
          <div className="flex gap-2" >
            <input type="text" value = { slogan } onChange = {(e) => setSlogan(e.target.value)} placeholder = "Slogan / Tagline (Opsional)" className = "w-full bg-gray-700 border-gray-600 rounded-lg p-3 focus:ring-2 focus:ring-cyan-500" />
              <button onClick={ handleGenerateSlogan } disabled = { isGeneratingSlogan } className = "px-4 bg-purple-600 hover:bg-purple-700 rounded-lg" title = "Buat Slogan" > { isGeneratingSlogan?<LoaderCircle className = "animate-spin"/> : <MessageSquareQuote/>}</button >
                <button onClick={ handleSpeakSlogan } disabled = { isSpeaking } className = "px-4 bg-cyan-600 hover:bg-cyan-700 rounded-lg" title = "Dengarkan Slogan" > { isSpeaking?<LoaderCircle className = "animate-spin"/> : <Volume2/>}</button >
                  </div>
                  < /div>
                  < /div>
                  < div >
                  <label className="block text-sm font-medium text-gray-300 mb-2" > 2. Rancang Konsep Logo < /label>
                    < div className = "bg-gray-900/50 p-4 rounded-lg space-y-4 border border-gray-700" >
                      <button onClick={ handleGenerateMagicPrompt } disabled = { isGeneratingMagic } className = "w-full flex items-center justify-center gap-3 p-3 bg-purple-600 hover:bg-purple-700 rounded-lg text-white font-semibold disabled:opacity-50" > { isGeneratingMagic?<LoaderCircle className = "animate-spin"/> : <Wand2/>} {isGeneratingMagic ? 'Menciptakan...' : '✨ Buat Konsep Ajaib'}</button >
                        <div className="flex gap-2" > <input type="text" value = { elements } onChange = {(e) => setElements(e.target.value)} placeholder = "Elemen visual" className = "w-full bg-gray-700 border-gray-600 rounded-lg p-2 focus:ring-2 focus:ring-cyan-500" /> <button onClick={ handleSuggestElements } disabled = { isSuggestingElements } className = "px-3 bg-gray-600 hover:bg-gray-500 rounded-lg" title = "Saran Elemen" > <Sparkles size={ 18 } /></button > </div>
                          < div className = "flex gap-2" >
                            <select value={ style } onChange = {(e) => setStyle(e.target.value)} className = "w-full bg-gray-700 border-gray-600 rounded-lg p-2 focus:ring-2 focus:ring-cyan-500 appearance-none" >
                            {
                              styleOptions.map(o => <option key={ o.value } value = { o.value } > { o.label } < /option>)}
                                < /select>
                                < /div>
                    { style === 'typography' && (
                                  <div className="flex items-center gap-2" > <Type size={ 18} className = "text-gray-400 ml-1" /> <select value={ font } onChange = {(e) => setFont(e.target.value)} className = "w-full bg-gray-700 border-gray-600 rounded-lg p-2 focus:ring-2 focus:ring-cyan-500 appearance-none" > {
                                    fontOptions.map(o => <option key={ o.value } value = { o.value } > { o.label } < /option>)}</select > </div>
                                    )
                                  }
                                    < div className = "flex gap-2" > <input type="text" value = { palette } onChange = {(e) => setPalette(e.target.value)} placeholder = "Palet warna" className = "w-full bg-gray-700 border-gray-600 rounded-lg p-2 focus:ring-2 focus:ring-cyan-500" /> <button onClick={ handleSuggestPalette } disabled = { isSuggestingPalette } className = "px-3 bg-gray-600 hover:bg-gray-500 rounded-lg" title = "Saran Palet Warna" > <Palette size={ 18 } /></button > </div>
                                      < div className = "flex items-center gap-2" > <Layers size={ 18 } className = "text-gray-400 ml-1" /> <select value={ background } onChange = {(e) => setBackground(e.target.value)} className = "w-full bg-gray-700 border-gray-600 rounded-lg p-2 focus:ring-2 focus:ring-cyan-500 appearance-none" > {
                                        backgroundOptions.map(o => <option key={ o.value } value = { o.value } > { o.label } < /option>)}</select > </div>
                                          < /div>
                                          < /div>
                                          < div >
                                          <label className="block text-sm font-medium text-gray-300 mb-2" > 3. Sempurnakan Detail & Gaya < /label>
                                          < div className = "p-4 bg-gray-900/50 rounded-lg border border-gray-700" >
                                          <button onClick={ handleSuggestDetails } disabled = { isSuggestingDetails } className = "w-full flex items-center justify-center gap-3 p-3 mb-4 bg-teal-600 hover:bg-teal-700 rounded-lg text-white font-semibold disabled:opacity-50" > { isSuggestingDetails?<LoaderCircle className = "animate-spin"/> : <Paintbrush/>} {isSuggestingDetails ? 'Menganalisis...' : '✨ Dapatkan Saran Detail & Gaya'}</button >
                                        <div className="grid grid-cols-2 sm:grid-cols-3 gap-x-4 gap-y-3" >
                                        { Object.keys(details).map(key => (<label key= { key } className = "flex items-center space-x-2 cursor-pointer text-gray-400 hover:text-white capitalize" > <input type="checkbox" name = { key } checked = { details[key]} onChange = { handleDetailChange } className = "h-4 w-4 rounded bg-gray-600 border-gray-500 text-cyan-500 focus:ring-cyan-600" /> <span>{ key.replace(/_/g, ' ') } < /span></label >))
                                      }
                                        < /div>
                                        < /div>
                                        < /div>
{
  error && <p className="text-red-400 text-sm mt-2 text-center" > { error } < /p>}
    < /div>
    < div className = "flex flex-col space-y-4" >
      <div className="flex flex-col bg-gray-900/70 rounded-lg p-4 border border-gray-700 h-full" >
        <h2 className="text-lg font-semibold text-white mb-2" > Prompt Final < /h2>
          < div className = "flex-grow bg-gray-900 rounded-md p-3 text-cyan-300 text-sm leading-relaxed min-h-[100px] font-mono" > { generatedPrompt } < /div>
            < button onClick = { handleCopy } className = {`mt-3 w-full flex items-center justify-center gap-2 p-2 rounded-lg font-semibold transition-all ${isCopied ? 'bg-green-600' : 'bg-cyan-800 hover:bg-cyan-700'}`
}> { isCopied?<Check size = { 18 } /> : <Clipboard size={ 18 } />}{isCopied ? 'Tersalin!' : 'Salin Prompt'}</button >
  </div>
  < div className = "flex flex-col bg-gray-900/70 rounded-lg p-4 border border-gray-700 h-full" >
    <h2 className="text-lg font-semibold text-white mb-2" > Hasil Logo & Analisis < /h2>
      < div className = "flex-grow flex items-center justify-center bg-gray-900 rounded-md aspect-square" >
        { isGeneratingImage?(<div className = "flex flex-col items-center gap-2 text-gray-400" > <LoaderCircle className="animate-spin" size = { 40} /> <span>Membuat logo...</ span > </div>) : 
generatedImage ? (<img src= { generatedImage } alt = "Logo yang dihasilkan" className = "object-contain w-full h-full rounded-md" />) :
(<div className= "flex flex-col items-center gap-2 text-gray-500" > <ImageIcon size={ 40 } /><span>Logo Anda akan muncul di sini</span > </div>)}
  < /div>
  < button onClick = { handleGenerateImage } disabled = { isGeneratingImage } className = "mt-3 w-full flex items-center justify-center gap-3 p-3 bg-gradient-to-r from-cyan-500 to-blue-500 hover:from-cyan-600 hover:to-blue-600 rounded-lg text-white font-bold disabled:opacity-50" > { isGeneratingImage?<LoaderCircle className = "animate-spin"/> : <Sparkles/>}Buat Logo Sekarang</button >
    { generatedImage && (<div className="mt-3" > <button onClick={ handleGetFeedback } disabled = { isGettingFeedback } className = "w-full flex items-center justify-center gap-3 p-3 bg-gray-700 hover:bg-gray-600 rounded-lg text-white font-semibold disabled:opacity-50" > { isGettingFeedback?<LoaderCircle className = "animate-spin"/> : <Lightbulb/>}Dapatkan Masukan AI</button > { logoFeedback && <div className="mt-3 p-3 bg-gray-900/80 rounded-lg text-sm text-gray-300 prose prose-invert prose-sm" dangerouslySetInnerHTML = {{ __html: logoFeedback.replace(/\n/g, '<br />') }} />}</div >)}
</div>
{
  generatedImage && (
    <div className="flex flex-col bg-gray-900/70 rounded-lg p-4 border border-gray-700 h-full" >
      <h2 className="text-lg font-semibold text-white mb-2" > Promosikan Brand Anda < /h2>
        < button onClick = { handleGeneratePost } disabled = { isGeneratingPost } className = "w-full flex items-center justify-center gap-3 p-3 bg-gradient-to-r from-purple-500 to-pink-500 hover:from-purple-600 hover:to-pink-600 rounded-lg text-white font-bold disabled:opacity-50" > { isGeneratingPost?<LoaderCircle className = "animate-spin"/> : <Send/>}Buat Postingan Pengumuman</button >
          { socialPost && <textarea readOnly value = { socialPost } className = "mt-3 w-full bg-gray-900 rounded-md p-3 text-gray-300 text-sm leading-relaxed h-40 font-mono" />}
</div>
                )}
</div>
  < /div>
  < /div>
  < /div>
  < /div>
  );
}

