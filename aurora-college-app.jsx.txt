import { useState, useEffect, useRef } from "react";

// ─── Initial Data ─────────────────────────────────────────────────────────────

const INITIAL_TEACHERS = [
  {
    id: "t1", name: "Dr. Priya Sharma", department: "Computer Science",
    email: "priya.sharma@aurora.edu", phone: "9876543210",
    subjects: ["Data Structures", "Algorithms", "Python Programming"],
    experience: "12 years", qualification: "Ph.D. Computer Science, IIT Delhi",
    image: "👩‍💻", overall_rating: 4.6, total_feedbacks: 38, monthly_trend: "up",
    created_at: new Date(Date.now() - 90 * 86400000).toISOString(),
    ratings_history: [
      { month: "Sep", avg: 4.2 }, { month: "Oct", avg: 4.3 },
      { month: "Nov", avg: 4.5 }, { month: "Dec", avg: 4.4 },
      { month: "Jan", avg: 4.6 }, { month: "Feb", avg: 4.6 }
    ]
  },
  {
    id: "t2", name: "Prof. Rajesh Kumar", department: "Mathematics",
    email: "rajesh.kumar@aurora.edu", phone: "9765432109",
    subjects: ["Calculus", "Linear Algebra", "Statistics"],
    experience: "8 years", qualification: "M.Sc. Mathematics, Osmania University",
    image: "👨‍🏫", overall_rating: 4.2, total_feedbacks: 27, monthly_trend: "same",
    created_at: new Date(Date.now() - 60 * 86400000).toISOString(),
    ratings_history: [
      { month: "Sep", avg: 4.0 }, { month: "Oct", avg: 4.1 },
      { month: "Nov", avg: 4.2 }, { month: "Dec", avg: 4.1 },
      { month: "Jan", avg: 4.2 }, { month: "Feb", avg: 4.2 }
    ]
  },
  {
    id: "t3", name: "Ms. Anita Reddy", department: "English",
    email: "anita.reddy@aurora.edu", phone: "9654321098",
    subjects: ["Business Communication", "Technical Writing", "Literature"],
    experience: "5 years", qualification: "M.A. English, Hyderabad University",
    image: "👩‍🏫", overall_rating: 4.8, total_feedbacks: 45, monthly_trend: "up",
    created_at: new Date(Date.now() - 30 * 86400000).toISOString(),
    ratings_history: [
      { month: "Sep", avg: 4.4 }, { month: "Oct", avg: 4.5 },
      { month: "Nov", avg: 4.6 }, { month: "Dec", avg: 4.7 },
      { month: "Jan", avg: 4.8 }, { month: "Feb", avg: 4.8 }
    ]
  }
];

const INITIAL_FEEDBACKS = [
  {
    id: "f1", teacher_id: "t1", student_id: "s1",
    teaching_clarity: 5, subject_knowledge: 5, accessibility: 4, engagement: 5, punctuality: 4,
    comments: "Dr. Priya explains complex algorithms with great clarity. The way she breaks down problems step by step is exceptional.",
    ai_sentiment: "positive",
    ai_strengths: ["Exceptional clarity in explaining complex topics", "Strong command of subject matter", "Engaging teaching methodology"],
    ai_improvements: ["Could provide more real-world examples", "More interactive sessions would help"],
    submitted_at: new Date(Date.now() - 5 * 86400000).toISOString()
  },
  {
    id: "f2", teacher_id: "t2", student_id: "s1",
    teaching_clarity: 4, subject_knowledge: 5, accessibility: 4, engagement: 3, punctuality: 5,
    comments: "Prof. Rajesh has deep knowledge of mathematics but sometimes the pace is a bit fast for students who need more time to understand.",
    ai_sentiment: "neutral",
    ai_strengths: ["Deep subject expertise", "Always punctual and organised", "Thorough coverage of syllabus"],
    ai_improvements: ["Slower pace for complex topics needed", "More engaging interactive elements"],
    submitted_at: new Date(Date.now() - 10 * 86400000).toISOString()
  },
  {
    id: "f3", teacher_id: "t3", student_id: "s2",
    teaching_clarity: 5, subject_knowledge: 5, accessibility: 5, engagement: 5, punctuality: 4,
    comments: "Ms. Anita is simply the best teacher I have had. Her communication classes are incredibly engaging and very practical.",
    ai_sentiment: "positive",
    ai_strengths: ["Outstanding classroom engagement", "Makes learning practical and applicable", "Highly approachable and supportive"],
    ai_improvements: ["Could assign more written exercises", "More group activities would be beneficial"],
    submitted_at: new Date(Date.now() - 3 * 86400000).toISOString()
  }
];

const INITIAL_RESOURCES = [
  { id: "r1", title: "Data Structures Question Bank 2024", subject: "Data Structures", type: "question_bank", semester: "3rd", stream: "B.Sc CS", year: null, file_size: "2.4 MB" },
  { id: "r2", title: "Calculus Question Bank", subject: "Calculus", type: "question_bank", semester: "1st", stream: "B.Sc", year: null, file_size: "1.8 MB" },
  { id: "r3", title: "Business Communication Notes", subject: "Business Communication", type: "question_bank", semester: "2nd", stream: "BBA", year: null, file_size: "3.1 MB" },
  { id: "r4", title: "Python Programming Previous Year 2023", subject: "Python", type: "previous_year", semester: "4th", stream: "B.Sc CS", year: "2023", file_size: "980 KB" },
  { id: "r5", title: "Statistics Exam Paper 2022", subject: "Statistics", type: "previous_year", semester: "5th", stream: "B.Sc", year: "2022", file_size: "1.2 MB" },
  { id: "r6", title: "Linear Algebra Previous Year 2023", subject: "Linear Algebra", type: "previous_year", semester: "3rd", stream: "B.Sc", year: "2023", file_size: "1.5 MB" }
];

const INITIAL_STUDENTS = [
  { id: "s1", username: "Arjun Patel", email: "arjun@student.aurora.edu", roll_number: "23CS001", password: "password123", created_at: new Date().toISOString() },
  { id: "s2", username: "Sneha Rao", email: "sneha@student.aurora.edu", roll_number: "23CS002", password: "password123", created_at: new Date().toISOString() }
];

// ─── Storage ──────────────────────────────────────────────────────────────────

const load = (key, fallback) => {
  try { const d = localStorage.getItem(key); return d ? JSON.parse(d) : fallback; }
  catch { return fallback; }
};
const save = (key, val) => { try { localStorage.setItem(key, JSON.stringify(val)); } catch {} };

const initDB = () => {
  if (!localStorage.getItem("aurora_v3")) {
    save("aurora_teachers", INITIAL_TEACHERS);
    save("aurora_feedbacks", INITIAL_FEEDBACKS);
    save("aurora_students", INITIAL_STUDENTS);
    save("aurora_resources", INITIAL_RESOURCES);
    save("aurora_v3", "1");
  }
};

// ─── AI ───────────────────────────────────────────────────────────────────────

const callClaude = async (ratings, comments, apiKey) => {
  const avg = (Object.values(ratings).reduce((a, b) => a + b, 0) / 5).toFixed(1);
  const res = await fetch("https://api.anthropic.com/v1/messages", {
    method: "POST",
    headers: { "Content-Type": "application/json", "x-api-key": apiKey, "anthropic-version": "2023-06-01" },
    body: JSON.stringify({
      model: "claude-sonnet-4-5-20250929",
      max_tokens: 500,
      messages: [{
        role: "user",
        content: `Analyse student feedback for a college lecturer.\nRatings out of 5 — Teaching Clarity: ${ratings.teachingClarity}, Subject Knowledge: ${ratings.subjectKnowledge}, Accessibility: ${ratings.accessibility}, Engagement: ${ratings.engagement}, Punctuality: ${ratings.punctuality}. Average: ${avg}.\nComments: "${comments}"\nReply ONLY with valid JSON (no markdown):\n{"sentiment":"positive|neutral|negative","strengths":["s1","s2","s3"],"improvements":["i1","i2"],"summary":"one sentence"}`
      }]
    })
  });
  if (!res.ok) throw new Error("API call failed");
  const data = await res.json();
  return JSON.parse(data.content[0].text.replace(/```json|```/g, "").trim());
};

const mockAI = (ratings) => {
  const avg = Object.values(ratings).reduce((a, b) => a + b, 0) / 5;
  return {
    sentiment: avg >= 4 ? "positive" : avg >= 3 ? "neutral" : "negative",
    strengths: ["Good subject knowledge", "Clear communication", "Supportive attitude"],
    improvements: ["More interactive sessions needed", "Additional practice examples would help"],
    summary: "Overall a satisfactory teaching experience."
  };
};

// ─── Design Tokens ────────────────────────────────────────────────────────────

const C = {
  blue: "#2563eb", blueDark: "#1d4ed8", blueLight: "#eff6ff", blueBorder: "#bfdbfe",
  green: "#16a34a", greenLight: "#f0fdf4", greenBorder: "#bbf7d0",
  red: "#dc2626", redLight: "#fef2f2", redBorder: "#fecaca",
  amber: "#d97706", amberLight: "#fffbeb",
  purple: "#7c3aed", purpleLight: "#faf5ff",
  gray50: "#f9fafb", gray100: "#f3f4f6", gray200: "#e5e7eb",
  gray400: "#9ca3af", gray500: "#6b7280", gray700: "#374151", gray900: "#111827",
  white: "#ffffff"
};

// ─── Atoms ────────────────────────────────────────────────────────────────────

function Stars({ rating, size = 14 }) {
  return (
    <span style={{ fontSize: size, letterSpacing: 1 }}>
      {[1, 2, 3, 4, 5].map(i => (
        <span key={i} style={{ color: i <= Math.round(rating) ? "#f59e0b" : C.gray200 }}>★</span>
      ))}
    </span>
  );
}

function Pill({ children, bg, color }) {
  return (
    <span style={{ background: bg, color, padding: "2px 10px", borderRadius: 99, fontSize: 12, fontWeight: 600, display: "inline-block" }}>
      {children}
    </span>
  );
}

function Bar({ value, max = 5, color = C.blue }) {
  return (
    <div style={{ height: 7, background: C.gray100, borderRadius: 99, overflow: "hidden" }}>
      <div style={{ width: `${(value / max) * 100}%`, height: "100%", background: color, borderRadius: 99, transition: "width .4s" }} />
    </div>
  );
}

function Toast({ msg, type, onClose }) {
  useEffect(() => { const t = setTimeout(onClose, 3500); return () => clearTimeout(t); }, [onClose]);
  const bg = type === "error" ? C.red : type === "warning" ? C.amber : C.green;
  return (
    <div style={{ position: "fixed", top: 18, right: 18, zIndex: 9999, background: bg, color: "#fff", padding: "11px 18px", borderRadius: 12, fontWeight: 600, fontSize: 14, boxShadow: "0 4px 20px rgba(0,0,0,.25)", maxWidth: 340 }}>
      {msg}
    </div>
  );
}

function Modal({ title, onClose, children }) {
  return (
    <div style={{ position: "fixed", inset: 0, zIndex: 500, background: "rgba(0,0,0,.45)", display: "flex", alignItems: "center", justifyContent: "center", padding: 16 }}
      onClick={onClose}>
      <div style={{ background: C.white, borderRadius: 16, padding: 24, width: "100%", maxWidth: 460, boxShadow: "0 20px 60px rgba(0,0,0,.3)" }}
        onClick={e => e.stopPropagation()}>
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 18 }}>
          <span style={{ fontWeight: 700, fontSize: 17, color: C.gray900 }}>{title}</span>
          <button onClick={onClose} style={{ background: "none", border: "none", fontSize: 22, cursor: "pointer", color: C.gray400, lineHeight: 1 }}>×</button>
        </div>
        {children}
      </div>
    </div>
  );
}

function Btn({ children, onClick, variant = "primary", disabled, full, style: extra = {} }) {
  const variants = {
    primary: { background: C.blue, color: C.white, border: "none" },
    secondary: { background: C.gray100, color: C.gray700, border: "none" },
    danger: { background: C.red, color: C.white, border: "none" },
    outline: { background: C.white, color: C.blue, border: `1px solid ${C.blue}` }
  };
  return (
    <button onClick={onClick} disabled={disabled}
      style={{
        ...variants[variant], padding: "9px 18px", borderRadius: 10, fontSize: 14,
        fontWeight: 600, cursor: disabled ? "not-allowed" : "pointer",
        opacity: disabled ? .55 : 1, width: full ? "100%" : undefined,
        fontFamily: "inherit", transition: "opacity .15s", ...extra
      }}>
      {children}
    </button>
  );
}

function Field({ label, value, onChange, type = "text", placeholder, required, rows }) {
  const base = { width: "100%", padding: "8px 12px", borderRadius: 9, border: `1px solid ${C.gray200}`, fontSize: 14, color: C.gray900, outline: "none", fontFamily: "inherit", boxSizing: "border-box", background: C.white };
  return (
    <div style={{ marginBottom: 14 }}>
      {label && <label style={{ display: "block", fontSize: 13, fontWeight: 600, color: C.gray700, marginBottom: 5 }}>{label}{required && <span style={{ color: C.red }}> *</span>}</label>}
      {rows
        ? <textarea value={value} onChange={e => onChange(e.target.value)} placeholder={placeholder} rows={rows} style={{ ...base, resize: "vertical" }} />
        : <input type={type} value={value} onChange={e => onChange(e.target.value)} placeholder={placeholder} style={base} />
      }
    </div>
  );
}

function TrendChart({ data }) {
  if (!data || data.length < 2) return <p style={{ color: C.gray400, fontSize: 13 }}>No trend data available</p>;
  const vals = data.map(d => d.avg);
  const lo = Math.min(...vals) - 0.4, hi = Math.max(...vals) + 0.4;
  const W = 280, H = 72;
  const px = (i) => (i / (vals.length - 1)) * (W - 28) + 14;
  const py = (v) => H - ((v - lo) / (hi - lo)) * (H - 16) - 8;
  const path = vals.map((v, i) => `${i === 0 ? "M" : "L"} ${px(i)} ${py(v)}`).join(" ");
  return (
    <svg width="100%" viewBox={`0 0 ${W} ${H + 20}`} style={{ display: "block" }}>
      <path d={path} fill="none" stroke={C.blue} strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round" />
      {vals.map((v, i) => (
        <g key={i}>
          <circle cx={px(i)} cy={py(v)} r="4" fill={C.blue} />
          <text x={px(i)} y={H + 18} textAnchor="middle" fontSize="10" fill={C.gray400}>{data[i].month}</text>
        </g>
      ))}
    </svg>
  );
}

// ─── App Root ─────────────────────────────────────────────────────────────────

export default function App() {
  const [user, setUser] = useState(null);
  const [teachers, setTeachers] = useState([]);
  const [feedbacks, setFeedbacks] = useState([]);
  const [students, setStudents] = useState([]);
  const [resources, setResources] = useState([]);
  const [apiKey, setApiKey] = useState("");
  const [toast, setToast] = useState(null);

  useEffect(() => {
    initDB();
    setTeachers(load("aurora_teachers", INITIAL_TEACHERS));
    setFeedbacks(load("aurora_feedbacks", INITIAL_FEEDBACKS));
    setStudents(load("aurora_students", INITIAL_STUDENTS));
    setResources(load("aurora_resources", INITIAL_RESOURCES));
    setApiKey(localStorage.getItem("aurora_ak") || "");
  }, []);

  const xt = (t) => { setTeachers(t); save("aurora_teachers", t); };
  const xf = (f) => { setFeedbacks(f); save("aurora_feedbacks", f); };
  const xs = (s) => { setStudents(s); save("aurora_students", s); };
  const xk = (k) => { setApiKey(k); localStorage.setItem("aurora_ak", k); };

  const notify = (msg, type = "success") => setToast({ msg, type });

  return (
    <div style={{ fontFamily: "'Segoe UI', system-ui, sans-serif", minHeight: "100vh", background: C.gray50, color: C.gray900 }}>
      {toast && <Toast msg={toast.msg} type={toast.type} onClose={() => setToast(null)} />}
      {!user
        ? <LoginPage students={students} onSaveStudents={xs} onLogin={setUser} notify={notify} />
        : user.role === "admin"
          ? <AdminApp user={user} teachers={teachers} feedbacks={feedbacks} students={students}
              resources={resources} onTeachers={xt} apiKey={apiKey} onApiKey={xk}
              notify={notify} onLogout={() => setUser(null)} />
          : <StudentApp user={user} teachers={teachers} feedbacks={feedbacks} resources={resources}
              onTeachers={xt} onFeedbacks={xf} apiKey={apiKey} onApiKey={xk}
              notify={notify} onLogout={() => setUser(null)} />
      }
    </div>
  );
}

// ─── Login ────────────────────────────────────────────────────────────────────

function LoginPage({ students, onSaveStudents, onLogin, notify }) {
  const [role, setRole] = useState("student");
  const [mode, setMode] = useState("login");
  const [id, setId] = useState("");
  const [pw, setPw] = useState("");
  const [reg, setReg] = useState({ name: "", email: "", roll: "", pw: "", pw2: "" });

  const adminLogin = () => {
    if (id.trim() === "admin" && pw === "admin123") {
      onLogin({ id: "admin", role: "admin", name: "Administrator" });
    } else notify("Incorrect username or password", "error");
  };

  const studentLogin = () => {
    if (!id.trim() || !pw.trim()) { notify("Please fill all fields", "error"); return; }
    const s = students.find(x => (x.roll_number === id.trim() || x.email === id.trim()) && x.password === pw);
    if (s) onLogin({ id: s.id, role: "student", name: s.username });
    else notify("Invalid credentials. Check roll number / email and password.", "error");
  };

  const register = () => {
    const { name, email, roll, pw: p, pw2 } = reg;
    if (!name || !email || !roll || !p) { notify("All fields are required", "error"); return; }
    if (p !== pw2) { notify("Passwords do not match", "error"); return; }
    if (p.length < 6) { notify("Password must be at least 6 characters", "error"); return; }
    if (students.find(s => s.roll_number === roll.trim())) { notify("Roll number already exists", "error"); return; }
    if (students.find(s => s.email === email.trim())) { notify("Email already registered", "error"); return; }
    const ns = { id: `s${Date.now()}`, username: name.trim(), email: email.trim(), roll_number: roll.trim(), password: p, created_at: new Date().toISOString() };
    onSaveStudents([...students, ns]);
    notify("Account created! Please log in.");
    setMode("login"); setReg({ name: "", email: "", roll: "", pw: "", pw2: "" });
  };

  const card = { background: C.white, borderRadius: 20, padding: 32, width: "100%", maxWidth: 420, boxShadow: "0 8px 40px rgba(37,99,235,.12)" };
  const tabBtn = (active) => ({
    flex: 1, padding: "8px 0", border: "none", borderRadius: 9, fontSize: 14, fontWeight: 600,
    cursor: "pointer", transition: "all .18s",
    background: active ? C.white : "transparent",
    color: active ? C.blue : C.gray500,
    boxShadow: active ? "0 1px 6px rgba(0,0,0,.1)" : "none"
  });

  return (
    <div style={{ minHeight: "100vh", display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center", background: "linear-gradient(135deg,#1e3a8a,#3730a3 50%,#6d28d9)", padding: 16 }}>
      <div style={{ textAlign: "center", marginBottom: 28 }}>
        <div style={{ fontSize: 52, marginBottom: 8 }}>🎓</div>
        <h1 style={{ color: C.white, fontSize: 24, fontWeight: 800, margin: 0 }}>Aurora's Degree & PG College</h1>
        <p style={{ color: "#c7d2fe", fontSize: 13, margin: "5px 0 0" }}>Student Feedback Management Portal</p>
      </div>

      <div style={card}>
        {/* Role toggle */}
        <div style={{ display: "flex", background: C.gray100, borderRadius: 11, padding: 4, marginBottom: 24 }}>
          <button style={tabBtn(role === "student")} onClick={() => { setRole("student"); setMode("login"); setId(""); setPw(""); }}>🎓 Student</button>
          <button style={tabBtn(role === "admin")} onClick={() => { setRole("admin"); setMode("login"); setId(""); setPw(""); }}>🛡️ Admin</button>
        </div>

        {/* Student sub-tabs */}
        {role === "student" && (
          <div style={{ display: "flex", borderBottom: `1px solid ${C.gray200}`, marginBottom: 20 }}>
            {[["login", "Login"], ["register", "Register"]].map(([v, l]) => (
              <button key={v} onClick={() => setMode(v)}
                style={{ flex: 1, padding: "8px 0", border: "none", background: "none", fontSize: 14, fontWeight: 600, cursor: "pointer", color: mode === v ? C.blue : C.gray400, borderBottom: `2px solid ${mode === v ? C.blue : "transparent"}`, marginBottom: -1, fontFamily: "inherit" }}>
                {l}
              </button>
            ))}
          </div>
        )}

        {/* Admin form */}
        {role === "admin" && (
          <>
            <Field label="Username" value={id} onChange={setId} placeholder="Enter username" />
            <Field label="Password" type="password" value={pw} onChange={setPw} placeholder="Enter password" />
            <Btn full onClick={adminLogin}>Sign In as Admin</Btn>
          </>
        )}

        {/* Student login */}
        {role === "student" && mode === "login" && (
          <>
            <Field label="Roll Number or Email" value={id} onChange={setId} placeholder="e.g. 23CS001" />
            <Field label="Password" type="password" value={pw} onChange={setPw} placeholder="Enter password" />
            <Btn full onClick={studentLogin}>Sign In</Btn>
          </>
        )}

        {/* Student register */}
        {role === "student" && mode === "register" && (
          <>
            <Field label="Full Name" value={reg.name} onChange={v => setReg(p => ({ ...p, name: v }))} placeholder="Your full name" required />
            <Field label="Email Address" type="email" value={reg.email} onChange={v => setReg(p => ({ ...p, email: v }))} placeholder="you@email.com" required />
            <Field label="Roll Number" value={reg.roll} onChange={v => setReg(p => ({ ...p, roll: v }))} placeholder="e.g. 23CS001" required />
            <Field label="Password" type="password" value={reg.pw} onChange={v => setReg(p => ({ ...p, pw: v }))} placeholder="Minimum 6 characters" required />
            <Field label="Confirm Password" type="password" value={reg.pw2} onChange={v => setReg(p => ({ ...p, pw2: v }))} placeholder="Repeat password" required />
            <Btn full onClick={register}>Create Account</Btn>
          </>
        )}
      </div>
    </div>
  );
}

// ─── Shared Header ────────────────────────────────────────────────────────────

function Header({ user, onLogout, apiKey, onApiKey }) {
  const [showModal, setShowModal] = useState(false);
  const [kInput, setKInput] = useState("");

  const openModal = () => { setKInput(apiKey); setShowModal(true); };
  const saveKey = () => { onApiKey(kInput.trim()); setShowModal(false); };

  return (
    <header style={{ background: C.white, borderBottom: `1px solid ${C.gray200}`, padding: "0 24px", height: 58, display: "flex", alignItems: "center", justifyContent: "space-between", position: "sticky", top: 0, zIndex: 200, boxShadow: "0 1px 8px rgba(0,0,0,.06)" }}>
      <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
        <span style={{ fontSize: 26 }}>🎓</span>
        <div>
          <div style={{ fontWeight: 800, fontSize: 15, color: "#1e3a8a", lineHeight: 1.2 }}>Aurora's College</div>
          <div style={{ fontSize: 11, color: C.gray400 }}>Feedback Portal</div>
        </div>
      </div>

      <div style={{ display: "flex", alignItems: "center", gap: 8 }}>
        <button onClick={openModal}
          style={{ padding: "5px 12px", borderRadius: 8, fontSize: 12, fontWeight: 600, cursor: "pointer", border: `1px solid ${apiKey ? C.greenBorder : "#fde68a"}`, background: apiKey ? C.greenLight : C.amberLight, color: apiKey ? C.green : C.amber }}>
          {apiKey ? "✓ AI Enabled" : "⚙ Set API Key"}
        </button>

        <div style={{ display: "flex", alignItems: "center", gap: 8, paddingLeft: 10, borderLeft: `1px solid ${C.gray200}` }}>
          <div style={{ width: 30, height: 30, borderRadius: "50%", background: C.blue, color: C.white, display: "flex", alignItems: "center", justifyContent: "center", fontWeight: 700, fontSize: 13 }}>
            {user.name.charAt(0).toUpperCase()}
          </div>
          <span style={{ fontSize: 13, fontWeight: 600, color: C.gray900 }}>{user.name}</span>
          <button onClick={onLogout}
            style={{ padding: "5px 12px", borderRadius: 8, fontSize: 12, fontWeight: 600, cursor: "pointer", border: `1px solid ${C.gray200}`, background: C.gray50, color: C.gray500, fontFamily: "inherit" }}>
            Logout
          </button>
        </div>
      </div>

      {showModal && (
        <Modal title="Claude AI API Key" onClose={() => setShowModal(false)}>
          <p style={{ fontSize: 13, color: C.gray500, marginBottom: 16 }}>Enter your Anthropic API key to enable AI-powered feedback analysis. Leave blank to use basic analysis.</p>
          <Field label="API Key" type="password" value={kInput} onChange={setKInput} placeholder="sk-ant-..." />
          <div style={{ display: "flex", gap: 10 }}>
            <Btn onClick={saveKey} style={{ flex: 1 }}>Save</Btn>
            <Btn variant="secondary" onClick={() => { onApiKey(""); setKInput(""); setShowModal(false); }}>Clear Key</Btn>
          </div>
        </Modal>
      )}
    </header>
  );
}

// ─── Student App ──────────────────────────────────────────────────────────────

function StudentApp({ user, teachers, feedbacks, resources, onTeachers, onFeedbacks, apiKey, onApiKey, notify, onLogout }) {
  const [tab, setTab] = useState("lecturers");
  const [profile, setProfile] = useState(null);

  if (profile) {
    const t = teachers.find(x => x.id === profile);
    if (!t) { setProfile(null); return null; }
    return (
      <>
        <Header user={user} onLogout={onLogout} apiKey={apiKey} onApiKey={onApiKey} />
        <TeacherProfile teacher={t} allFeedbacks={feedbacks} allTeachers={teachers}
          user={user} onTeachers={onTeachers} onFeedbacks={onFeedbacks}
          apiKey={apiKey} notify={notify} onBack={() => setProfile(null)} />
      </>
    );
  }

  return (
    <>
      <Header user={user} onLogout={onLogout} apiKey={apiKey} onApiKey={onApiKey} />
      <div style={{ background: C.white, borderBottom: `1px solid ${C.gray200}` }}>
        <div style={{ maxWidth: 1100, margin: "0 auto", padding: "0 20px", display: "flex" }}>
          {[["lecturers", "Lecturers"], ["question_bank", "Question Banks"], ["previous_year", "Previous Year Papers"]].map(([id, label]) => (
            <button key={id} onClick={() => setTab(id)}
              style={{ padding: "14px 20px", border: "none", background: "none", fontSize: 14, fontWeight: 600, cursor: "pointer", color: tab === id ? C.blue : C.gray500, borderBottom: `2px solid ${tab === id ? C.blue : "transparent"}`, fontFamily: "inherit" }}>
              {label}
            </button>
          ))}
        </div>
      </div>
      <div style={{ maxWidth: 1100, margin: "0 auto", padding: "28px 20px" }}>
        {tab === "lecturers" && <LecturersGrid teachers={teachers} feedbacks={feedbacks} onSelect={id => setProfile(id)} />}
        {tab === "question_bank" && <ResourcesGrid items={resources.filter(r => r.type === "question_bank")} />}
        {tab === "previous_year" && <ResourcesGrid items={resources.filter(r => r.type === "previous_year")} />}
      </div>
    </>
  );
}

// ─── Lecturers Grid ───────────────────────────────────────────────────────────

function LecturersGrid({ teachers, feedbacks, onSelect }) {
  const [q, setQ] = useState("");
  const [dept, setDept] = useState("All");
  const depts = ["All", ...new Set(teachers.map(t => t.department))];
  const list = teachers.filter(t =>
    (dept === "All" || t.department === dept) &&
    (t.name.toLowerCase().includes(q.toLowerCase()) || t.department.toLowerCase().includes(q.toLowerCase()))
  );

  return (
    <div>
      <div style={{ display: "flex", gap: 12, marginBottom: 24, flexWrap: "wrap" }}>
        <input value={q} onChange={e => setQ(e.target.value)} placeholder="Search by name or department..."
          style={{ flex: 1, minWidth: 200, padding: "9px 14px", borderRadius: 10, border: `1px solid ${C.gray200}`, fontSize: 14, outline: "none", background: C.white }} />
        <select value={dept} onChange={e => setDept(e.target.value)}
          style={{ padding: "9px 14px", borderRadius: 10, border: `1px solid ${C.gray200}`, fontSize: 14, background: C.white, outline: "none", minWidth: 160, fontFamily: "inherit" }}>
          {depts.map(d => <option key={d}>{d}</option>)}
        </select>
      </div>

      {list.length === 0
        ? <div style={{ textAlign: "center", padding: "64px 20px", color: C.gray400 }}><div style={{ fontSize: 44, marginBottom: 10 }}>🔍</div><p>No lecturers found</p></div>
        : (
          <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill, minmax(270px, 1fr))", gap: 16 }}>
            {list.map(t => (
              <LecturerCard key={t.id} teacher={t}
                count={feedbacks.filter(f => f.teacher_id === t.id).length}
                onClick={() => onSelect(t.id)} />
            ))}
          </div>
        )
      }
    </div>
  );
}

function LecturerCard({ teacher, count, onClick }) {
  const [hov, setHov] = useState(false);
  const trendColor = teacher.monthly_trend === "up" ? C.green : teacher.monthly_trend === "down" ? C.red : C.gray400;
  const trendIcon = teacher.monthly_trend === "up" ? "↑" : teacher.monthly_trend === "down" ? "↓" : "→";

  return (
    <div onClick={onClick} onMouseEnter={() => setHov(true)} onMouseLeave={() => setHov(false)}
      style={{ background: C.white, borderRadius: 16, padding: 20, border: `1px solid ${hov ? "#93c5fd" : C.gray200}`, boxShadow: hov ? "0 4px 20px rgba(37,99,235,.1)" : "0 1px 4px rgba(0,0,0,.04)", cursor: "pointer", transition: "all .18s" }}>
      <div style={{ display: "flex", gap: 14, marginBottom: 14, alignItems: "flex-start" }}>
        <div style={{ width: 50, height: 50, borderRadius: 14, background: C.blueLight, display: "flex", alignItems: "center", justifyContent: "center", fontSize: 28, flexShrink: 0 }}>
          {teacher.image}
        </div>
        <div style={{ flex: 1, minWidth: 0 }}>
          <div style={{ fontWeight: 700, color: hov ? C.blue : C.gray900, fontSize: 15, marginBottom: 2, whiteSpace: "nowrap", overflow: "hidden", textOverflow: "ellipsis" }}>
            {teacher.name}
          </div>
          <div style={{ fontSize: 13, color: C.blue, fontWeight: 500, marginBottom: 5 }}>{teacher.department}</div>
          <div style={{ display: "flex", alignItems: "center", gap: 6 }}>
            <Stars rating={teacher.overall_rating} />
            <span style={{ fontSize: 13, fontWeight: 700 }}>{teacher.overall_rating}</span>
            <span style={{ fontSize: 13, fontWeight: 700, color: trendColor }}>{trendIcon}</span>
          </div>
        </div>
      </div>
      <div style={{ display: "flex", flexWrap: "wrap", gap: 5, marginBottom: 12 }}>
        {teacher.subjects?.slice(0, 2).map(s => <Pill key={s} bg={C.blueLight} color={C.blue}>{s}</Pill>)}
        {(teacher.subjects?.length || 0) > 2 && <Pill bg={C.gray100} color={C.gray500}>+{teacher.subjects.length - 2} more</Pill>}
      </div>
      <div style={{ display: "flex", justifyContent: "space-between", fontSize: 13, paddingTop: 10, borderTop: `1px solid ${C.gray100}` }}>
        <span style={{ color: C.gray500 }}>💬 {count} reviews</span>
        <span style={{ color: C.blue, fontWeight: 600 }}>View Profile →</span>
      </div>
    </div>
  );
}

// ─── Teacher Profile ──────────────────────────────────────────────────────────

function TeacherProfile({ teacher, allFeedbacks, allTeachers, user, onTeachers, onFeedbacks, apiKey, notify, onBack }) {
  const [showForm, setShowForm] = useState(false);
  const [busy, setBusy] = useState(false);
  const [ratings, setRatings] = useState({ teachingClarity: 3, subjectKnowledge: 3, accessibility: 3, engagement: 3, punctuality: 3 });
  const [comment, setComment] = useState("");

  const tfbs = allFeedbacks.filter(f => f.teacher_id === teacher.id);
  const already = tfbs.some(f => f.student_id === user.id);

  const countMap = (arr) => arr.reduce((a, v) => ({ ...a, [v]: (a[v] || 0) + 1 }), {});
  const topOf = (arr, n) => Object.entries(countMap(arr)).sort((a, b) => b[1] - a[1]).slice(0, n).map(e => e[0]);
  const strengths = topOf(tfbs.flatMap(f => f.ai_strengths || []), 4);
  const improvements = topOf(tfbs.flatMap(f => f.ai_improvements || []), 3);
  const avg = (key) => tfbs.length ? tfbs.reduce((s, f) => s + (f[key] || 0), 0) / tfbs.length : 0;

  const cats = [
    ["teaching_clarity", "Teaching Clarity"],
    ["subject_knowledge", "Subject Knowledge"],
    ["accessibility", "Accessibility"],
    ["engagement", "Engagement"],
    ["punctuality", "Punctuality"]
  ];

  const submit = async () => {
    if (comment.trim().length < 50) { notify("Comments must be at least 50 characters", "error"); return; }
    setBusy(true);
    let ai;
    try { ai = apiKey ? await callClaude(ratings, comment.trim(), apiKey) : mockAI(ratings); }
    catch { ai = mockAI(ratings); }

    const fb = {
      id: `f${Date.now()}`, teacher_id: teacher.id, student_id: user.id,
      teaching_clarity: ratings.teachingClarity, subject_knowledge: ratings.subjectKnowledge,
      accessibility: ratings.accessibility, engagement: ratings.engagement, punctuality: ratings.punctuality,
      comments: comment.trim(), ai_sentiment: ai.sentiment, ai_strengths: ai.strengths,
      ai_improvements: ai.improvements, submitted_at: new Date().toISOString()
    };

    const newFbs = [...allFeedbacks, fb];
    onFeedbacks(newFbs);

    const tFbs = newFbs.filter(f => f.teacher_id === teacher.id);
    const newAvg = tFbs.reduce((s, f) => s + (f.teaching_clarity + f.subject_knowledge + f.accessibility + f.engagement + f.punctuality) / 5, 0) / tFbs.length;
    onTeachers(allTeachers.map(t => t.id === teacher.id ? { ...t, overall_rating: Math.round(newAvg * 10) / 10, total_feedbacks: tFbs.length } : t));

    setBusy(false); setShowForm(false); setComment(""); setRatings({ teachingClarity: 3, subjectKnowledge: 3, accessibility: 3, engagement: 3, punctuality: 3 });
    notify("Feedback submitted with AI analysis! ✨");
  };

  const S = { background: C.white, borderRadius: 16, padding: 22, border: `1px solid ${C.gray200}`, marginBottom: 16 };

  return (
    <div style={{ maxWidth: 860, margin: "0 auto", padding: "24px 20px" }}>
      <button onClick={onBack} style={{ background: "none", border: "none", color: C.blue, fontWeight: 600, fontSize: 14, cursor: "pointer", marginBottom: 18, padding: 0, fontFamily: "inherit" }}>
        ← Back to Lecturers
      </button>

      {/* Profile header */}
      <div style={{ ...S, background: "linear-gradient(135deg,#eff6ff,#eef2ff)", marginBottom: 16 }}>
        <div style={{ display: "flex", gap: 20, flexWrap: "wrap", alignItems: "flex-start" }}>
          <div style={{ width: 76, height: 76, borderRadius: 20, background: C.white, display: "flex", alignItems: "center", justifyContent: "center", fontSize: 42, boxShadow: "0 2px 12px rgba(0,0,0,.1)", flexShrink: 0 }}>
            {teacher.image}
          </div>
          <div style={{ flex: 1, minWidth: 180 }}>
            <h2 style={{ margin: "0 0 4px", fontSize: 22, fontWeight: 800, color: C.gray900 }}>{teacher.name}</h2>
            <div style={{ color: C.blue, fontWeight: 600, marginBottom: 10 }}>{teacher.department}</div>
            <div style={{ display: "flex", flexWrap: "wrap", gap: "5px 18px", fontSize: 13, color: C.gray500 }}>
              <span>📧 {teacher.email}</span>
              <span>📱 {teacher.phone}</span>
              <span>🎓 {teacher.qualification}</span>
              <span>⏱ {teacher.experience}</span>
            </div>
            <div style={{ display: "flex", flexWrap: "wrap", gap: 5, marginTop: 10 }}>
              {teacher.subjects?.map(s => <Pill key={s} bg={C.blueLight} color={C.blue}>{s}</Pill>)}
            </div>
          </div>
          <div style={{ textAlign: "center", background: C.white, borderRadius: 14, padding: "14px 22px", boxShadow: "0 2px 10px rgba(0,0,0,.07)", flexShrink: 0 }}>
            <div style={{ fontSize: 34, fontWeight: 900, color: C.blue }}>{teacher.overall_rating}</div>
            <Stars rating={teacher.overall_rating} size={16} />
            <div style={{ fontSize: 12, color: C.gray400, marginTop: 4 }}>{teacher.total_feedbacks} reviews</div>
          </div>
        </div>
      </div>

      <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, marginBottom: 16 }}>
        <div style={S}>
          <h3 style={{ margin: "0 0 16px", fontSize: 15, fontWeight: 700 }}>Rating Breakdown</h3>
          {cats.map(([key, label]) => (
            <div key={key} style={{ marginBottom: 12 }}>
              <div style={{ display: "flex", justifyContent: "space-between", fontSize: 13, marginBottom: 5 }}>
                <span style={{ color: C.gray700 }}>{label}</span>
                <span style={{ fontWeight: 700, color: C.blue }}>{avg(key).toFixed(1)}</span>
              </div>
              <Bar value={avg(key)} />
            </div>
          ))}
        </div>
        <div style={S}>
          <h3 style={{ margin: "0 0 16px", fontSize: 15, fontWeight: 700 }}>6-Month Trend</h3>
          <TrendChart data={teacher.ratings_history} />
        </div>
      </div>

      {(strengths.length > 0 || improvements.length > 0) && (
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, marginBottom: 16 }}>
          {strengths.length > 0 && (
            <div style={S}>
              <h3 style={{ margin: "0 0 14px", fontSize: 15, fontWeight: 700 }}>✨ Strengths (AI)</h3>
              {strengths.map(s => (
                <div key={s} style={{ display: "flex", gap: 8, marginBottom: 8, fontSize: 13 }}>
                  <span style={{ color: C.green, fontWeight: 700, flexShrink: 0 }}>✓</span>
                  <span style={{ color: C.gray700 }}>{s}</span>
                </div>
              ))}
            </div>
          )}
          {improvements.length > 0 && (
            <div style={S}>
              <h3 style={{ margin: "0 0 14px", fontSize: 15, fontWeight: 700 }}>💡 Improvements (AI)</h3>
              {improvements.map(s => (
                <div key={s} style={{ display: "flex", gap: 8, marginBottom: 8, fontSize: 13 }}>
                  <span style={{ color: C.amber, fontWeight: 700, flexShrink: 0 }}>!</span>
                  <span style={{ color: C.gray700 }}>{s}</span>
                </div>
              ))}
            </div>
          )}
        </div>
      )}

      {/* Feedback section */}
      {already ? (
        <div style={{ ...S, background: C.greenLight, border: `1px solid ${C.greenBorder}`, textAlign: "center", color: C.green, fontWeight: 600 }}>
          ✅ You have already submitted feedback for this lecturer.
        </div>
      ) : showForm ? (
        <div style={S}>
          <h3 style={{ margin: "0 0 20px", fontSize: 16, fontWeight: 700 }}>Submit Your Feedback</h3>
          {[
            ["teachingClarity", "Teaching Clarity"],
            ["subjectKnowledge", "Subject Knowledge"],
            ["accessibility", "Accessibility"],
            ["engagement", "Engagement"],
            ["punctuality", "Punctuality"]
          ].map(([key, label]) => (
            <div key={key} style={{ marginBottom: 18 }}>
              <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 6 }}>
                <label style={{ fontSize: 13, fontWeight: 600, color: C.gray700 }}>{label}</label>
                <span style={{ fontSize: 13, fontWeight: 700, color: C.blue, background: C.blueLight, padding: "2px 10px", borderRadius: 99 }}>{ratings[key]} / 5</span>
              </div>
              <input type="range" min="1" max="5" step="1" value={ratings[key]}
                onChange={e => setRatings(p => ({ ...p, [key]: +e.target.value }))}
                style={{ width: "100%", accentColor: C.blue }} />
              <div style={{ display: "flex", justifyContent: "space-between", fontSize: 11, color: C.gray400, marginTop: 2 }}>
                <span>Poor</span><span>Excellent</span>
              </div>
            </div>
          ))}
          <div style={{ marginBottom: 16 }}>
            <label style={{ display: "block", fontSize: 13, fontWeight: 600, color: C.gray700, marginBottom: 5 }}>
              Comments <span style={{ fontWeight: 400, color: C.gray400 }}>(minimum 50 characters)</span>
            </label>
            <textarea value={comment} onChange={e => setComment(e.target.value)}
              placeholder="Write your feedback about this lecturer..." rows={4}
              style={{ width: "100%", padding: "9px 12px", borderRadius: 10, border: `1px solid ${C.gray200}`, fontSize: 14, outline: "none", resize: "vertical", fontFamily: "inherit", boxSizing: "border-box" }} />
            <div style={{ textAlign: "right", fontSize: 12, marginTop: 3, color: comment.length >= 50 ? C.green : C.gray400 }}>
              {comment.length} characters {comment.length < 50 ? `(${50 - comment.length} more needed)` : "✓"}
            </div>
          </div>
          <div style={{ display: "flex", gap: 10 }}>
            <Btn onClick={submit} disabled={busy} style={{ flex: 1 }}>
              {busy ? "Analysing with AI…" : "Submit Feedback ✨"}
            </Btn>
            <Btn variant="secondary" onClick={() => setShowForm(false)}>Cancel</Btn>
          </div>
        </div>
      ) : (
        <button onClick={() => setShowForm(true)}
          style={{ width: "100%", padding: "14px 0", background: C.blue, color: C.white, border: "none", borderRadius: 14, fontSize: 15, fontWeight: 700, cursor: "pointer", marginBottom: 16, fontFamily: "inherit" }}>
          ✍️ Submit Feedback for {teacher.name}
        </button>
      )}

      {tfbs.length > 0 && (
        <div style={S}>
          <h3 style={{ margin: "0 0 16px", fontSize: 15, fontWeight: 700 }}>Recent Feedback</h3>
          <div style={{ display: "flex", flexDirection: "column", gap: 10 }}>
            {[...tfbs].reverse().slice(0, 5).map(f => (
              <div key={f.id} style={{ padding: 14, background: C.gray50, borderRadius: 12, border: `1px solid ${C.gray100}` }}>
                <div style={{ display: "flex", gap: 8, alignItems: "center", marginBottom: 7 }}>
                  <Pill bg={f.ai_sentiment === "positive" ? C.greenLight : f.ai_sentiment === "negative" ? C.redLight : C.gray100}
                    color={f.ai_sentiment === "positive" ? C.green : f.ai_sentiment === "negative" ? C.red : C.gray500}>
                    {f.ai_sentiment}
                  </Pill>
                  <span style={{ fontSize: 12, color: C.gray400 }}>{new Date(f.submitted_at).toLocaleDateString("en-IN")}</span>
                </div>
                <p style={{ margin: 0, fontSize: 13, color: C.gray700, fontStyle: "italic" }}>"{f.comments}"</p>
              </div>
            ))}
          </div>
        </div>
      )}
    </div>
  );
}

// ─── Resources Grid ───────────────────────────────────────────────────────────

function ResourcesGrid({ items }) {
  const [sub, setSub] = useState("All");
  const subs = ["All", ...new Set(items.map(r => r.subject))];
  const list = sub === "All" ? items : items.filter(r => r.subject === sub);

  return (
    <div>
      <div style={{ display: "flex", flexWrap: "wrap", gap: 8, marginBottom: 22 }}>
        {subs.map(s => (
          <button key={s} onClick={() => setSub(s)}
            style={{ padding: "6px 16px", borderRadius: 99, fontSize: 13, fontWeight: 600, cursor: "pointer", border: `1px solid ${sub === s ? C.blue : C.gray200}`, background: sub === s ? C.blue : C.white, color: sub === s ? C.white : C.gray700, fontFamily: "inherit", transition: "all .15s" }}>
            {s}
          </button>
        ))}
      </div>
      {list.length === 0
        ? <div style={{ textAlign: "center", padding: 60, color: C.gray400 }}><div style={{ fontSize: 44, marginBottom: 10 }}>📭</div><p>No resources found</p></div>
        : (
          <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill, minmax(270px, 1fr))", gap: 14 }}>
            {list.map(r => (
              <div key={r.id} style={{ background: C.white, borderRadius: 14, border: `1px solid ${C.gray200}`, padding: 18 }}>
                <div style={{ display: "flex", gap: 12, marginBottom: 12 }}>
                  <div style={{ width: 42, height: 42, borderRadius: 12, background: C.blueLight, display: "flex", alignItems: "center", justifyContent: "center", fontSize: 22, flexShrink: 0 }}>
                    {r.type === "question_bank" ? "📚" : "📋"}
                  </div>
                  <div>
                    <div style={{ fontWeight: 700, fontSize: 14, color: C.gray900, marginBottom: 6 }}>{r.title}</div>
                    <div style={{ display: "flex", flexWrap: "wrap", gap: 4 }}>
                      <Pill bg={C.blueLight} color={C.blue}>{r.subject}</Pill>
                      <Pill bg={C.purpleLight} color={C.purple}>Sem {r.semester}</Pill>
                      <Pill bg={C.gray100} color={C.gray500}>{r.stream}</Pill>
                      {r.year && <Pill bg={C.amberLight} color={C.amber}>{r.year}</Pill>}
                    </div>
                  </div>
                </div>
                <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", paddingTop: 10, borderTop: `1px solid ${C.gray100}` }}>
                  <span style={{ fontSize: 12, color: C.gray400 }}>📄 {r.file_size}</span>
                  <button style={{ padding: "5px 14px", background: C.blue, color: C.white, border: "none", borderRadius: 8, fontSize: 12, fontWeight: 600, cursor: "pointer", fontFamily: "inherit" }}>
                    ⬇ Download
                  </button>
                </div>
              </div>
            ))}
          </div>
        )
      }
    </div>
  );
}

// ─── Admin App ────────────────────────────────────────────────────────────────

function AdminApp({ user, teachers, feedbacks, students, onTeachers, apiKey, onApiKey, notify, onLogout }) {
  const [page, setPage] = useState("dashboard");
  const [editT, setEditT] = useState(null);

  const nav = [
    ["dashboard", "📊 Dashboard"],
    ["teachers", "👩‍🏫 Teachers"],
    ["add", "➕ Add Teacher"],
    ["bulk", "📤 Bulk Upload"],
    ["analytics", "📈 Analytics"]
  ];

  return (
    <>
      <Header user={user} onLogout={onLogout} apiKey={apiKey} onApiKey={onApiKey} />
      <div style={{ display: "flex", minHeight: "calc(100vh - 58px)" }}>
        <aside style={{ width: 196, background: C.white, borderRight: `1px solid ${C.gray200}`, padding: "12px 0", flexShrink: 0 }}>
          {nav.map(([id, label]) => (
            <button key={id} onClick={() => { setPage(id); if (id !== "add") setEditT(null); }}
              style={{ display: "block", width: "100%", padding: "11px 18px", textAlign: "left", border: "none", background: page === id ? C.blueLight : "transparent", color: page === id ? C.blue : C.gray500, fontWeight: page === id ? 700 : 500, fontSize: 14, cursor: "pointer", borderLeft: `3px solid ${page === id ? C.blue : "transparent"}`, fontFamily: "inherit", transition: "all .15s" }}>
              {label}
            </button>
          ))}
        </aside>
        <main style={{ flex: 1, padding: "28px 28px", overflowY: "auto" }}>
          {page === "dashboard" && <AdminDash teachers={teachers} feedbacks={feedbacks} students={students} />}
          {page === "teachers" && <TeachersList teachers={teachers} feedbacks={feedbacks} onTeachers={onTeachers} notify={notify} onEdit={t => { setEditT(t); setPage("edit"); }} />}
          {(page === "add" || page === "edit") && <TeacherForm teacher={editT} teachers={teachers} onTeachers={onTeachers} notify={notify} onDone={() => { setPage("teachers"); setEditT(null); }} />}
          {page === "bulk" && <BulkUpload teachers={teachers} onTeachers={onTeachers} notify={notify} />}
          {page === "analytics" && <Analytics teachers={teachers} feedbacks={feedbacks} students={students} />}
        </main>
      </div>
    </>
  );
}

// ─── Admin Dashboard ──────────────────────────────────────────────────────────

function AdminDash({ teachers, feedbacks, students }) {
  const globalAvg = feedbacks.length
    ? (feedbacks.reduce((s, f) => s + (f.teaching_clarity + f.subject_knowledge + f.accessibility + f.engagement + f.punctuality) / 5, 0) / feedbacks.length).toFixed(1)
    : "—";

  const stats = [
    { icon: "👩‍🏫", label: "Total Teachers", value: teachers.length, bg: C.blueLight, color: C.blue },
    { icon: "💬", label: "Total Feedback", value: feedbacks.length, bg: C.greenLight, color: C.green },
    { icon: "🎓", label: "Registered Students", value: students.length, bg: C.purpleLight, color: C.purple },
    { icon: "⭐", label: "Average Rating", value: globalAvg, bg: C.amberLight, color: C.amber }
  ];

  const S = { background: C.white, borderRadius: 14, border: `1px solid ${C.gray200}`, padding: 20 };

  return (
    <div>
      <h2 style={{ margin: "0 0 22px", fontSize: 22, fontWeight: 800 }}>Dashboard</h2>
      <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill, minmax(170px, 1fr))", gap: 14, marginBottom: 26 }}>
        {stats.map(s => (
          <div key={s.label} style={{ ...S, background: s.bg }}>
            <div style={{ fontSize: 28, marginBottom: 6 }}>{s.icon}</div>
            <div style={{ fontSize: 30, fontWeight: 900, color: s.color }}>{s.value}</div>
            <div style={{ fontSize: 13, color: C.gray500, marginTop: 2 }}>{s.label}</div>
          </div>
        ))}
      </div>
      <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16 }}>
        <div style={S}>
          <h3 style={{ margin: "0 0 16px", fontSize: 15, fontWeight: 700 }}>Top Rated Teachers</h3>
          {[...teachers].sort((a, b) => b.overall_rating - a.overall_rating).slice(0, 5).map(t => (
            <div key={t.id} style={{ display: "flex", alignItems: "center", gap: 10, marginBottom: 10 }}>
              <span style={{ fontSize: 24 }}>{t.image}</span>
              <div style={{ flex: 1 }}>
                <div style={{ fontSize: 13, fontWeight: 600 }}>{t.name}</div>
                <div style={{ fontSize: 12, color: C.gray400 }}>{t.department}</div>
              </div>
              <div style={{ display: "flex", alignItems: "center", gap: 3, fontSize: 13 }}>
                <span style={{ color: "#f59e0b" }}>★</span>
                <span style={{ fontWeight: 700 }}>{t.overall_rating}</span>
              </div>
            </div>
          ))}
        </div>
        <div style={S}>
          <h3 style={{ margin: "0 0 16px", fontSize: 15, fontWeight: 700 }}>Recent Feedback</h3>
          {feedbacks.length === 0
            ? <p style={{ color: C.gray400, fontSize: 13 }}>No feedback yet</p>
            : [...feedbacks].reverse().slice(0, 5).map(f => {
              const t = teachers.find(x => x.id === f.teacher_id);
              return (
                <div key={f.id} style={{ display: "flex", alignItems: "center", gap: 10, marginBottom: 10 }}>
                  <span style={{ width: 8, height: 8, borderRadius: "50%", flexShrink: 0, background: f.ai_sentiment === "positive" ? C.green : f.ai_sentiment === "negative" ? C.red : C.gray200 }} />
                  <div style={{ flex: 1, minWidth: 0 }}>
                    <div style={{ fontSize: 13, fontWeight: 600, whiteSpace: "nowrap", overflow: "hidden", textOverflow: "ellipsis" }}>{t?.name || "—"}</div>
                    <div style={{ fontSize: 11, color: C.gray400 }}>{new Date(f.submitted_at).toLocaleDateString("en-IN")}</div>
                  </div>
                  <Pill bg={f.ai_sentiment === "positive" ? C.greenLight : f.ai_sentiment === "negative" ? C.redLight : C.gray100}
                    color={f.ai_sentiment === "positive" ? C.green : f.ai_sentiment === "negative" ? C.red : C.gray500}>
                    {f.ai_sentiment}
                  </Pill>
                </div>
              );
            })}
        </div>
      </div>
    </div>
  );
}

// ─── Teachers List ────────────────────────────────────────────────────────────

function TeachersList({ teachers, feedbacks, onTeachers, notify, onEdit }) {
  const [q, setQ] = useState("");
  const [del, setDel] = useState(null);
  const list = teachers.filter(t => t.name.toLowerCase().includes(q.toLowerCase()) || t.department.toLowerCase().includes(q.toLowerCase()));

  return (
    <div>
      <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 18 }}>
        <h2 style={{ margin: 0, fontSize: 22, fontWeight: 800 }}>All Teachers</h2>
      </div>
      <input value={q} onChange={e => setQ(e.target.value)} placeholder="Search teachers..."
        style={{ width: "100%", padding: "9px 14px", borderRadius: 10, border: `1px solid ${C.gray200}`, fontSize: 14, outline: "none", background: C.white, marginBottom: 16, boxSizing: "border-box", fontFamily: "inherit" }} />
      <div style={{ display: "flex", flexDirection: "column", gap: 10 }}>
        {list.map(t => {
          const fc = feedbacks.filter(f => f.teacher_id === t.id).length;
          return (
            <div key={t.id} style={{ background: C.white, border: `1px solid ${C.gray200}`, borderRadius: 14, padding: "14px 18px", display: "flex", alignItems: "center", gap: 14, flexWrap: "wrap" }}>
              <span style={{ fontSize: 32, flexShrink: 0 }}>{t.image}</span>
              <div style={{ flex: 1, minWidth: 160 }}>
                <div style={{ display: "flex", alignItems: "center", gap: 8, flexWrap: "wrap" }}>
                  <span style={{ fontWeight: 700, fontSize: 15 }}>{t.name}</span>
                  <Pill bg={C.blueLight} color={C.blue}>{t.department}</Pill>
                </div>
                <div style={{ fontSize: 12, color: C.gray400, marginTop: 3 }}>{t.email} • {t.experience}</div>
                <div style={{ display: "flex", alignItems: "center", gap: 5, marginTop: 4 }}>
                  <Stars rating={t.overall_rating} size={13} />
                  <span style={{ fontSize: 12, color: C.gray500 }}>{t.overall_rating} · {fc} reviews</span>
                </div>
              </div>
              <div style={{ display: "flex", gap: 8 }}>
                <Btn variant="outline" onClick={() => onEdit(t)} style={{ padding: "6px 14px", fontSize: 13 }}>Edit</Btn>
                <Btn variant="danger" onClick={() => setDel(t.id)} style={{ padding: "6px 14px", fontSize: 13 }}>Delete</Btn>
              </div>
            </div>
          );
        })}
        {list.length === 0 && <p style={{ textAlign: "center", color: C.gray400, padding: "40px 0" }}>No teachers found</p>}
      </div>
      {del && (
        <Modal title="Confirm Delete" onClose={() => setDel(null)}>
          <p style={{ fontSize: 14, color: C.gray500, marginBottom: 20 }}>This will permanently remove the teacher. Continue?</p>
          <div style={{ display: "flex", gap: 10 }}>
            <Btn variant="danger" onClick={() => { onTeachers(teachers.filter(t => t.id !== del)); notify("Teacher deleted"); setDel(null); }} style={{ flex: 1 }}>Delete</Btn>
            <Btn variant="secondary" onClick={() => setDel(null)} style={{ flex: 1 }}>Cancel</Btn>
          </div>
        </Modal>
      )}
    </div>
  );
}

// ─── Teacher Form ─────────────────────────────────────────────────────────────

function TeacherForm({ teacher, teachers, onTeachers, notify, onDone }) {
  const isEdit = !!teacher;
  const emojis = ["👩‍💻", "👨‍🏫", "👩‍🏫", "👨‍💼", "👩‍🔬", "👨‍🔬", "🧑‍🏫", "👩‍💼"];
  const [f, setF] = useState({
    name: teacher?.name || "", department: teacher?.department || "",
    email: teacher?.email || "", phone: teacher?.phone || "",
    subjects: teacher?.subjects?.join(", ") || "",
    experience: teacher?.experience || "", qualification: teacher?.qualification || "",
    image: teacher?.image || "👩‍🏫"
  });
  const upd = (k) => (v) => setF(p => ({ ...p, [k]: v }));

  const save = () => {
    if (!f.name.trim() || !f.department.trim() || !f.email.trim()) { notify("Name, Department and Email are required", "error"); return; }
    const obj = {
      ...(teacher || { id: `t${Date.now()}`, overall_rating: 0, total_feedbacks: 0, monthly_trend: "same", created_at: new Date().toISOString(), ratings_history: [] }),
      ...f, subjects: f.subjects.split(",").map(s => s.trim()).filter(Boolean)
    };
    onTeachers(isEdit ? teachers.map(t => t.id === obj.id ? obj : t) : [...teachers, obj]);
    notify(isEdit ? "Teacher updated!" : "Teacher added!");
    onDone();
  };

  return (
    <div>
      <h2 style={{ margin: "0 0 22px", fontSize: 22, fontWeight: 800 }}>{isEdit ? "Edit Teacher" : "Add New Teacher"}</h2>
      <div style={{ background: C.white, borderRadius: 16, border: `1px solid ${C.gray200}`, padding: 24, maxWidth: 580 }}>
        <div style={{ marginBottom: 14 }}>
          <label style={{ display: "block", fontSize: 13, fontWeight: 600, color: C.gray700, marginBottom: 8 }}>Profile Emoji</label>
          <div style={{ display: "flex", gap: 8, flexWrap: "wrap" }}>
            {emojis.map(e => (
              <button key={e} onClick={() => upd("image")(e)}
                style={{ width: 42, height: 42, borderRadius: 10, fontSize: 22, cursor: "pointer", border: `2px solid ${f.image === e ? C.blue : C.gray200}`, background: f.image === e ? C.blueLight : C.gray50, transform: f.image === e ? "scale(1.12)" : "scale(1)", transition: "all .15s", fontFamily: "inherit" }}>
                {e}
              </button>
            ))}
          </div>
        </div>
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: "0 16px" }}>
          <Field label="Full Name" value={f.name} onChange={upd("name")} placeholder="Dr. Jane Doe" required />
          <Field label="Department" value={f.department} onChange={upd("department")} placeholder="Computer Science" required />
          <Field label="Email" type="email" value={f.email} onChange={upd("email")} placeholder="jane@aurora.edu" required />
          <Field label="Phone" value={f.phone} onChange={upd("phone")} placeholder="9876543210" />
          <Field label="Experience" value={f.experience} onChange={upd("experience")} placeholder="5 years" />
          <Field label="Qualification" value={f.qualification} onChange={upd("qualification")} placeholder="M.Sc., Ph.D." />
        </div>
        <Field label="Subjects (comma-separated)" value={f.subjects} onChange={upd("subjects")} placeholder="Math, Statistics, Calculus" />
        <div style={{ display: "flex", gap: 10 }}>
          <Btn onClick={save} style={{ flex: 1 }}>{isEdit ? "Save Changes" : "Add Teacher"}</Btn>
          <Btn variant="secondary" onClick={onDone}>Cancel</Btn>
        </div>
      </div>
    </div>
  );
}

// ─── Bulk Upload ──────────────────────────────────────────────────────────────

function BulkUpload({ teachers, onTeachers, notify }) {
  const [preview, setPreview] = useState(null);
  const [err, setErr] = useState("");
  const ref = useRef();

  const template = JSON.stringify([{ name: "Dr. Example", department: "Science", email: "example@aurora.edu", phone: "9876543210", subjects: ["Physics", "Chemistry"], experience: "5 years", qualification: "Ph.D.", image: "👩‍🏫" }], null, 2);

  const download = () => {
    const a = document.createElement("a");
    a.href = URL.createObjectURL(new Blob([template], { type: "application/json" }));
    a.download = "teachers_template.json";
    a.click();
  };

  const pick = (e) => {
    const file = e.target.files[0]; if (!file) return;
    const r = new FileReader();
    r.onload = ev => {
      try {
        const data = JSON.parse(ev.target.result);
        if (!Array.isArray(data)) throw new Error("File must contain a JSON array");
        const valid = data.filter(t => t.name && t.department && t.email);
        if (!valid.length) throw new Error("No valid entries found. Each needs name, department and email.");
        setPreview(valid); setErr("");
      } catch (ex) { setErr(ex.message); setPreview(null); }
    };
    r.readAsText(file);
  };

  const importAll = () => {
    const now = Date.now();
    const list = preview.map((t, i) => ({
      id: `t${now}_${i}`, ...t,
      subjects: Array.isArray(t.subjects) ? t.subjects : (t.subjects || "").split(",").map(s => s.trim()).filter(Boolean),
      overall_rating: 0, total_feedbacks: 0, monthly_trend: "same", created_at: new Date().toISOString(), ratings_history: []
    }));
    onTeachers([...teachers, ...list]);
    notify(`Imported ${list.length} teacher(s) successfully!`);
    setPreview(null);
    if (ref.current) ref.current.value = "";
  };

  const S = { background: C.white, borderRadius: 14, border: `1px solid ${C.gray200}`, padding: 22 };

  return (
    <div>
      <h2 style={{ margin: "0 0 22px", fontSize: 22, fontWeight: 800 }}>Bulk Upload Teachers</h2>
      <div style={{ maxWidth: 540 }}>
        <div style={{ ...S, background: C.blueLight, border: `1px solid ${C.blueBorder}`, marginBottom: 16 }}>
          <p style={{ margin: 0, fontSize: 13, color: C.blueDark, lineHeight: 1.7 }}>
            <strong>Steps:</strong> Download the JSON template → Fill in teacher details → Upload the file → Preview & confirm.
            Each entry requires <em>name</em>, <em>department</em>, and <em>email</em>.
          </p>
        </div>
        <Btn variant="outline" onClick={download} style={{ marginBottom: 16 }}>⬇ Download JSON Template</Btn>
        <div onClick={() => ref.current?.click()}
          onMouseEnter={e => e.currentTarget.style.borderColor = "#93c5fd"}
          onMouseLeave={e => e.currentTarget.style.borderColor = C.gray200}
          style={{ ...S, border: `2px dashed ${C.gray200}`, cursor: "pointer", textAlign: "center", padding: "40px 20px", marginBottom: 14, transition: "border-color .15s" }}>
          <div style={{ fontSize: 38, marginBottom: 8 }}>📁</div>
          <p style={{ margin: 0, fontWeight: 600, color: C.gray700 }}>Click to select a JSON file</p>
          <p style={{ margin: "4px 0 0", fontSize: 13, color: C.gray400 }}>Accepts .json files only</p>
          <input ref={ref} type="file" accept=".json" onChange={pick} style={{ display: "none" }} />
        </div>
        {err && <div style={{ background: C.redLight, border: `1px solid ${C.redBorder}`, borderRadius: 10, padding: "12px 16px", color: C.red, fontSize: 13, marginBottom: 14 }}>❌ {err}</div>}
        {preview && (
          <div style={S}>
            <h3 style={{ margin: "0 0 12px", fontSize: 15, fontWeight: 700 }}>Preview — {preview.length} teachers</h3>
            <div style={{ maxHeight: 220, overflowY: "auto", marginBottom: 14 }}>
              {preview.map((t, i) => (
                <div key={i} style={{ display: "flex", alignItems: "center", gap: 10, padding: "8px 12px", background: C.gray50, borderRadius: 8, marginBottom: 6 }}>
                  <span style={{ fontSize: 22 }}>{t.image || "👩‍🏫"}</span>
                  <div>
                    <div style={{ fontWeight: 600, fontSize: 13 }}>{t.name}</div>
                    <div style={{ fontSize: 12, color: C.gray400 }}>{t.department} · {t.email}</div>
                  </div>
                  <span style={{ marginLeft: "auto", color: C.green, fontWeight: 700, fontSize: 13 }}>✓ Valid</span>
                </div>
              ))}
            </div>
            <Btn full onClick={importAll}>Import {preview.length} Teacher(s)</Btn>
          </div>
        )}
      </div>
    </div>
  );
}

// ─── Analytics ────────────────────────────────────────────────────────────────

function Analytics({ teachers, feedbacks, students }) {
  const deptC = teachers.reduce((a, t) => ({ ...a, [t.department]: (a[t.department] || 0) + 1 }), {});
  const sentC = feedbacks.reduce((a, f) => ({ ...a, [f.ai_sentiment]: (a[f.ai_sentiment] || 0) + 1 }), {});
  const globalAvg = feedbacks.length
    ? (feedbacks.reduce((s, f) => s + (f.teaching_clarity + f.subject_knowledge + f.accessibility + f.engagement + f.punctuality) / 5, 0) / feedbacks.length).toFixed(1)
    : 0;

  const S = { background: C.white, borderRadius: 14, border: `1px solid ${C.gray200}`, padding: 22 };

  const BarRow = ({ label, value, max, color }) => (
    <div style={{ marginBottom: 12 }}>
      <div style={{ display: "flex", justifyContent: "space-between", fontSize: 13, marginBottom: 5 }}>
        <span style={{ color: C.gray700 }}>{label}</span>
        <span style={{ fontWeight: 700, color }}>{typeof value === "number" ? (value % 1 !== 0 ? value.toFixed(1) : value) : value}</span>
      </div>
      <Bar value={value} max={max} color={color} />
    </div>
  );

  return (
    <div>
      <h2 style={{ margin: "0 0 22px", fontSize: 22, fontWeight: 800 }}>Analytics & Insights</h2>
      <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16 }}>
        <div style={S}>
          <h3 style={{ margin: "0 0 16px", fontSize: 15, fontWeight: 700 }}>Teachers by Department</h3>
          {Object.entries(deptC).map(([d, n]) => <BarRow key={d} label={d} value={n} max={teachers.length} color={C.blue} />)}
        </div>
        <div style={S}>
          <h3 style={{ margin: "0 0 16px", fontSize: 15, fontWeight: 700 }}>Feedback Sentiment</h3>
          {[["positive", "Positive", C.green], ["neutral", "Neutral", C.gray400], ["negative", "Negative", C.red]].map(([k, l, c]) => {
            const n = sentC[k] || 0;
            const p = feedbacks.length ? Math.round((n / feedbacks.length) * 100) : 0;
            return <BarRow key={k} label={l} value={p} max={100} color={c} />;
          })}
        </div>
        <div style={S}>
          <h3 style={{ margin: "0 0 16px", fontSize: 15, fontWeight: 700 }}>Average Category Ratings</h3>
          {feedbacks.length > 0
            ? [["teaching_clarity", "Teaching Clarity"], ["subject_knowledge", "Subject Knowledge"], ["accessibility", "Accessibility"], ["engagement", "Engagement"], ["punctuality", "Punctuality"]].map(([k, l]) => {
              const v = feedbacks.reduce((s, f) => s + (f[k] || 0), 0) / feedbacks.length;
              return <BarRow key={k} label={l} value={v} max={5} color={C.purple} />;
            })
            : <p style={{ color: C.gray400, fontSize: 13 }}>No feedback data yet</p>
          }
        </div>
        <div style={S}>
          <h3 style={{ margin: "0 0 16px", fontSize: 15, fontWeight: 700 }}>Key Numbers</h3>
          {[
            { icon: "⭐", label: "College Avg Rating", value: globalAvg },
            { icon: "💬", label: "Total Feedbacks", value: feedbacks.length },
            { icon: "🏛️", label: "Departments", value: Object.keys(deptC).length },
            { icon: "🎓", label: "Students Registered", value: students.length }
          ].map(s => (
            <div key={s.label} style={{ display: "flex", alignItems: "center", gap: 12, marginBottom: 10, padding: "10px 14px", background: C.gray50, borderRadius: 10 }}>
              <span style={{ fontSize: 22 }}>{s.icon}</span>
              <span style={{ flex: 1, fontSize: 13, color: C.gray700 }}>{s.label}</span>
              <span style={{ fontWeight: 800, fontSize: 18, color: C.gray900 }}>{s.value}</span>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}
