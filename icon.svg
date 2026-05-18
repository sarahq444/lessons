import { useState, useEffect, useRef } from "react";

const STORAGE_KEY = "lessons-journal-entries";
const THEME_KEY = "lessons-theme";

const LIGHT = {
  bg: "#f7f5f0", text: "#1a1a1a", muted: "#999", faint: "#bbb",
  border: "#e0ddd6", borderLight: "#e8e5de", inputBorder: "#d0cdc6",
  searchBorder: "#1a1a1a", btnBg: "#1a1a1a", btnText: "#f7f5f0",
  btnDisabledBg: "#e0ddd6", btnDisabledText: "#bbb", cancelBorder: "1px solid #ddd",
  highlight: "#d4f0c4", highlightText: "#1a1a1a",
  navActive: "#1a1a1a", navInactive: "#aaa",
  iconBg: "#f7f5f0", iconStroke: "#1a1a1a", iconFill: "#1a1a1a", iconHole: "#f7f5f0",
};

const DARK = {
  bg: "#161614", text: "#e8e4dc", muted: "#666", faint: "#555",
  border: "#2a2825", borderLight: "#252320", inputBorder: "#3a3733",
  searchBorder: "#e8e4dc", btnBg: "#e8e4dc", btnText: "#161614",
  btnDisabledBg: "#2a2825", btnDisabledText: "#555", cancelBorder: "1px solid #3a3733",
  highlight: "#2a3a1e", highlightText: "#e8e4dc",
  navActive: "#e8e4dc", navInactive: "#555",
  iconBg: "#161614", iconStroke: "#e8e4dc", iconFill: "#e8e4dc", iconHole: "#161614",
};

function formatDate(dateStr) {
  const d = new Date(dateStr);
  return d.toLocaleDateString("en-US", { weekday: "long", month: "long", day: "numeric", year: "numeric" });
}

function highlight(text, query, t) {
  if (!query.trim()) return text;
  const escaped = query.replace(/[.*+?^${}()|[\]\\]/g, "\\$&");
  const parts = text.split(new RegExp(`(${escaped})`, "gi"));
  return parts.map((part, i) =>
    part.toLowerCase() === query.toLowerCase()
      ? <mark key={i} style={{ background: t.highlight, color: t.highlightText, borderRadius: "2px", padding: "0 2px" }}>{part}</mark>
      : part
  );
}

function MoonIcon() {
  return <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round"><path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z" /></svg>;
}
function SunIcon() {
  return <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round"><circle cx="12" cy="12" r="5" /><line x1="12" y1="1" x2="12" y2="3" /><line x1="12" y1="21" x2="12" y2="23" /><line x1="4.22" y1="4.22" x2="5.64" y2="5.64" /><line x1="18.36" y1="18.36" x2="19.78" y2="19.78" /><line x1="1" y1="12" x2="3" y2="12" /><line x1="21" y1="12" x2="23" y2="12" /><line x1="4.22" y1="19.78" x2="5.64" y2="18.36" /><line x1="18.36" y1="5.64" x2="19.78" y2="4.22" /></svg>;
}

function EyeIcon({ t }) {
  return (
    <svg width="36" height="36" viewBox="0 0 1024 1024" xmlns="http://www.w3.org/2000/svg" style={{ flexShrink: 0 }}>
      <rect width="1024" height="1024" fill={t.iconBg} rx="200" />
      <g fill="none" stroke={t.iconStroke} strokeLinecap="round" strokeLinejoin="round">
        <path d="M180 512 C 250 380, 380 290, 512 285 C 644 290, 774 380, 844 512" strokeWidth="18" />
        <path d="M180 512 C 250 640, 380 730, 512 735 C 644 730, 774 640, 844 512" strokeWidth="18" />
        <circle cx="512" cy="512" r="130" strokeWidth="16" fill={t.iconBg} />
        <circle cx="512" cy="512" r="68" fill={t.iconFill} stroke="none" />
        <circle cx="480" cy="478" r="22" fill={t.iconHole} stroke="none" />
        <circle cx="538" cy="496" r="10" fill={t.iconHole} stroke="none" />
        <path d="M 512 660 C 512 660, 500 700, 470 740 C 455 762, 440 775, 430 795" strokeWidth="14" />
        <path d="M 430 795 C 420 815, 445 820, 460 805" strokeWidth="12" />
        <line x1="300" y1="422" x2="288" y2="398" strokeWidth="9" />
        <line x1="360" y1="392" x2="352" y2="366" strokeWidth="9" />
        <line x1="425" y1="372" x2="420" y2="345" strokeWidth="9" />
        <line x1="512" y1="363" x2="512" y2="335" strokeWidth="9" />
        <line x1="599" y1="372" x2="604" y2="345" strokeWidth="9" />
        <line x1="664" y1="392" x2="672" y2="366" strokeWidth="9" />
        <line x1="724" y1="422" x2="736" y2="398" strokeWidth="9" />
      </g>
    </svg>
  );
}

export default function LessonsJournal() {
  const [entries, setEntries] = useState([]);
  const [view, setView] = useState("write");
  const [draft, setDraft] = useState("");
  const [searchQuery, setSearchQuery] = useState("");
  const [editingId, setEditingId] = useState(null);
  const [editText, setEditText] = useState("");
  const [saved, setSaved] = useState(false);
  const [dark, setDark] = useState(false);
  const textareaRef = useRef(null);
  const searchRef = useRef(null);
  const t = dark ? DARK : LIGHT;

  useEffect(() => {
    try {
      const stored = localStorage.getItem(STORAGE_KEY);
      if (stored) setEntries(JSON.parse(stored));
      const th = localStorage.getItem(THEME_KEY);
      if (th) setDark(th === "dark");
    } catch {}
  }, []);

  const toggleDark = () => {
    const next = !dark;
    setDark(next);
    try { localStorage.setItem(THEME_KEY, next ? "dark" : "light"); } catch {}
  };

  const saveEntries = (e) => {
    try { localStorage.setItem(STORAGE_KEY, JSON.stringify(e)); } catch {}
    setEntries(e);
  };

  const handleSave = () => {
    if (!draft.trim()) return;
    saveEntries([{ id: Date.now(), text: draft.trim(), date: new Date().toISOString() }, ...entries]);
    setDraft(""); setSaved(true); setTimeout(() => setSaved(false), 1800);
  };

  const handleDelete = (id) => saveEntries(entries.filter(e => e.id !== id));

  const handleEdit = (id) => {
    saveEntries(entries.map(e => e.id === id ? { ...e, text: editText.trim() } : e));
    setEditingId(null); setEditText("");
  };

  const filtered = searchQuery.trim()
    ? entries.filter(e => e.text.toLowerCase().includes(searchQuery.toLowerCase()))
    : entries;

  useEffect(() => {
    if (view === "write" && textareaRef.current) textareaRef.current.focus();
    if (view === "search" && searchRef.current) searchRef.current.focus();
  }, [view]);

  return (
    <div style={{ minHeight: "100vh", background: t.bg, fontFamily: "'Georgia', serif", color: t.text, display: "flex", flexDirection: "column", transition: "background 0.2s, color 0.2s" }}>

      <header style={{ padding: "28px 32px 16px", display: "flex", alignItems: "center", gap: "14px", borderBottom: `1.5px solid ${t.border}` }}>
        <EyeIcon t={t} />
        <div style={{ display: "flex", alignItems: "baseline", gap: "10px", flex: 1 }}>
          <h1 style={{ fontSize: "22px", fontWeight: "400", letterSpacing: "0.01em", margin: 0 }}>lessons</h1>
          <span style={{ fontSize: "13px", color: t.muted, fontFamily: "monospace" }}>{entries.length} {entries.length === 1 ? "entry" : "entries"}</span>
        </div>
        <button onClick={toggleDark} style={{ background: "none", border: "none", cursor: "pointer", color: t.muted, padding: "4px", display: "flex", alignItems: "center" }}>
          {dark ? <SunIcon /> : <MoonIcon />}
        </button>
      </header>

      <nav style={{ display: "flex", padding: "0 32px", borderBottom: `1.5px solid ${t.border}` }}>
        {["write", "all", "search"].map(v => (
          <button key={v} onClick={() => setView(v)} style={{
            background: "none", border: "none",
            borderBottom: view === v ? `2px solid ${t.navActive}` : "2px solid transparent",
            padding: "12px 16px 10px", cursor: "pointer", fontSize: "13px",
            color: view === v ? t.navActive : t.navInactive,
            fontFamily: "inherit", letterSpacing: "0.06em", marginBottom: "-1.5px", transition: "color 0.15s",
          }}>{v}</button>
        ))}
      </nav>

      <main style={{ flex: 1, padding: "28px 32px", maxWidth: "680px", width: "100%", margin: "0 auto", boxSizing: "border-box" }}>

        {view === "write" && (
          <div>
            <p style={{ fontSize: "13px", color: t.faint, margin: "0 0 16px", fontStyle: "italic" }}>
              {new Date().toLocaleDateString("en-US", { weekday: "long", month: "long", day: "numeric" })}
            </p>
            <textarea ref={textareaRef} value={draft} onChange={e => setDraft(e.target.value)}
              placeholder="what did we learn today..." rows={8}
              style={{ width: "100%", border: "none", borderBottom: `1.5px solid ${t.inputBorder}`, background: "transparent", fontFamily: "'Georgia', serif", fontSize: "17px", lineHeight: "1.75", color: t.text, outline: "none", resize: "none", padding: "0 0 12px", boxSizing: "border-box", caretColor: t.muted }}
              onKeyDown={e => { if ((e.metaKey || e.ctrlKey) && e.key === "Enter") handleSave(); }}
            />
            <div style={{ display: "flex", alignItems: "center", gap: "16px", marginTop: "16px" }}>
              <button onClick={handleSave} disabled={!draft.trim()} style={{ background: draft.trim() ? t.btnBg : t.btnDisabledBg, color: draft.trim() ? t.btnText : t.btnDisabledText, border: "none", padding: "10px 24px", fontSize: "13px", fontFamily: "inherit", letterSpacing: "0.08em", cursor: draft.trim() ? "pointer" : "default", borderRadius: "2px", transition: "all 0.15s" }}>save</button>
              {saved && <span style={{ fontSize: "13px", color: t.muted, fontStyle: "italic" }}>saved ✓</span>}
              <span style={{ fontSize: "12px", color: t.faint, marginLeft: "auto" }}>⌘↵ to save</span>
            </div>
          </div>
        )}

        {view === "all" && (
          <div>
            {entries.length === 0 && <p style={{ color: t.faint, fontStyle: "italic", fontSize: "15px" }}>no entries yet — write your first lesson.</p>}
            {entries.map(entry => (
              <div key={entry.id} style={{ borderBottom: `1px solid ${t.borderLight}`, padding: "20px 0" }}>
                <p style={{ fontSize: "12px", color: t.faint, margin: "0 0 8px", fontFamily: "monospace", letterSpacing: "0.04em" }}>{formatDate(entry.date)}</p>
                {editingId === entry.id ? (
                  <div>
                    <textarea value={editText} onChange={e => setEditText(e.target.value)} rows={5} style={{ width: "100%", border: "none", borderBottom: `1.5px solid ${t.inputBorder}`, background: "transparent", fontFamily: "'Georgia', serif", fontSize: "16px", lineHeight: "1.75", color: t.text, outline: "none", resize: "none", padding: "0 0 8px", boxSizing: "border-box" }} />
                    <div style={{ display: "flex", gap: "12px", marginTop: "8px" }}>
                      <button onClick={() => handleEdit(entry.id)} style={mkBtn(t.btnBg, t.btnText)}>save</button>
                      <button onClick={() => setEditingId(null)} style={mkBtn("transparent", t.muted, t.cancelBorder)}>cancel</button>
                    </div>
                  </div>
                ) : (
                  <div>
                    <p style={{ fontSize: "16px", lineHeight: "1.75", margin: "0 0 10px", whiteSpace: "pre-wrap" }}>{entry.text}</p>
                    <div style={{ display: "flex", gap: "16px" }}>
                      <button onClick={() => { setEditingId(entry.id); setEditText(entry.text); }} style={mkGhost(t.faint)}>edit</button>
                      <button onClick={() => handleDelete(entry.id)} style={mkGhost("#c07070")}>delete</button>
                    </div>
                  </div>
                )}
              </div>
            ))}
          </div>
        )}

        {view === "search" && (
          <div>
            <input ref={searchRef} type="text" value={searchQuery} onChange={e => setSearchQuery(e.target.value)}
              placeholder="search your lessons..."
              style={{ width: "100%", border: "none", borderBottom: `1.5px solid ${t.searchBorder}`, background: "transparent", fontFamily: "'Georgia', serif", fontSize: "17px", color: t.text, outline: "none", padding: "0 0 10px", boxSizing: "border-box", marginBottom: "24px" }}
            />
            {searchQuery.trim() && <p style={{ fontSize: "12px", color: t.faint, fontFamily: "monospace", marginBottom: "20px" }}>{filtered.length} {filtered.length === 1 ? "result" : "results"}</p>}
            {searchQuery.trim() && filtered.length === 0 && <p style={{ color: t.faint, fontStyle: "italic", fontSize: "15px" }}>nothing found.</p>}
            {filtered.map(entry => (
              <div key={entry.id} style={{ borderBottom: `1px solid ${t.borderLight}`, padding: "20px 0" }}>
                <p style={{ fontSize: "12px", color: t.faint, margin: "0 0 8px", fontFamily: "monospace", letterSpacing: "0.04em" }}>{formatDate(entry.date)}</p>
                <p style={{ fontSize: "16px", lineHeight: "1.75", margin: 0, whiteSpace: "pre-wrap" }}>{highlight(entry.text, searchQuery, t)}</p>
              </div>
            ))}
          </div>
        )}
      </main>
    </div>
  );
}

const mkGhost = (color) => ({ background: "none", border: "none", padding: "0", cursor: "pointer", fontSize: "12px", color, fontFamily: "'Georgia', serif", letterSpacing: "0.05em" });
const mkBtn = (bg, color, border) => ({ background: bg, color, border: border || "none", padding: "8px 20px", fontSize: "12px", fontFamily: "'Georgia', serif", letterSpacing: "0.06em", cursor: "pointer", borderRadius: "2px" });
