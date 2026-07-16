import { useState, useEffect, useRef } from "react";
import {
  Terminal, Github, Linkedin, Mail, ExternalLink, ChevronRight,
  Circle, CheckCircle2, Cloud, Database, Code2, Award, Briefcase,
  ArrowUpRight, GitBranch, Activity
} from "lucide-react";

const BOOT_LINES = [
  "$ ssh ruthwik@production",
  "Connecting to profile server... OK",
  "Loading identity.json ... done",
  "Mounting /projects (3 deployed) ... done",
  "Checking uptime ... 24/7, available for hire",
  "$ ./launch --profile=recruiter",
];

const STATS = [
  { label: "Production apps shipped", value: "03" },
  { label: "Hitbullseye percentile", value: "98.93" },
  { label: "DSA problems solved", value: "200+" },
  { label: "AWS CI/CD pipelines built", value: "01" },
];

const STACK = [
  { group: "Languages", icon: Code2, items: ["C", "C++", "Java", "JavaScript"] },
  { group: "Frontend", icon: Activity, items: ["React.js", "Tailwind CSS", "HTML5", "CSS3"] },
  { group: "Backend", icon: GitBranch, items: ["Node.js", "Express.js", "JWT", "REST APIs"] },
  { group: "Data", icon: Database, items: ["MySQL", "MongoDB", "PostgreSQL", "SQLite"] },
  { group: "Cloud / DevOps", icon: Cloud, items: ["AWS (ECS, ECR, ALB)", "Docker", "Kubernetes", "GitHub Actions"] },
];

const PROJECTS = [
  {
    id: "talentbridge",
    tag: "01 · PRODUCT",
    name: "TalentBridge",
    role: "Secure Enterprise Hiring Platform",
    problem: "Hiring pipelines break when access isn't isolated and screening is manual.",
    solution: "A centralized recruitment system with role-locked permissions and automated candidate evaluation.",
    metrics: [
      "RBAC + JWT — Admin / Recruiter / Candidate isolation",
      "ATS-inspired screening engine cuts manual triage",
      "Modular Node.js/Express services, MySQL-backed",
    ],
    stack: ["React.js", "Node.js", "Express.js", "MySQL", "JWT"],
    link: "https://talent-bridge-secure-enterprise-ful.vercel.app",
    status: "LIVE",
  },
  {
    id: "cloudforge",
    tag: "02 · INFRASTRUCTURE",
    name: "CloudForge",
    role: "Production-Grade AWS CI/CD Infrastructure",
    problem: "Manual deploys don't scale, and configuration drift kills reliability.",
    solution: "A fully automated, cloud-native pipeline that removes both from the equation.",
    metrics: [
      "GitHub Actions → Docker → ECR → ECS Fargate",
      "ALB + auto-scaling + health checks for resilience",
      "CloudWatch + SNS for real-time observability",
    ],
    stack: ["AWS ECS", "Docker", "GitHub Actions", "CloudWatch"],
    link: null,
    status: "DEPLOYED",
  },
  {
    id: "expense",
    tag: "03 · PRODUCT",
    name: "Shared Expense Manager",
    role: "Financial Reconciliation Platform",
    problem: "Group expenses lose transparency without automated auditing.",
    solution: "Reconciles, simplifies, and visualizes shared finances end-to-end.",
    metrics: [
      "CSV anomaly detection — duplicates, currency mismatches",
      "Debt-simplification algorithm for optimal settlements",
      "Live React + Recharts analytics dashboards",
    ],
    stack: ["React.js", "Vite", "Node.js", "SQLite", "Recharts"],
    link: "https://shared-expense-manager-xi.vercel.app/",
    status: "LIVE",
  },
];

const EXPERIENCE = [
  {
    company: "JPMorgan Chase & Co.",
    role: "Advanced Software Engineering — Forage",
    points: [
      "Built microservices with Spring Boot, improving modularity and scalability",
      "Integrated Apache Kafka for real-time, high-volume transaction streaming",
      "Designed REST APIs, improving backend communication efficiency ~30%",
    ],
  },
  {
    company: "Commonwealth Bank",
    role: "Software Engineering — Forage",
    points: [
      "Built backend services with .NET and MongoDB",
      "Developed responsive UI components with React and Redux",
      "Ran API testing via Postman, reducing API errors ~20%",
    ],
  },
];

const TERMINAL_HELP = [
  "help        show available commands",
  "whoami      short bio",
  "skills      list core technologies",
  "projects    list shipped projects",
  "experience  list industry simulations",
  "contact     get in touch",
  "sudo hire   ./run this one",
  "clear       clear the terminal",
];

function runCommand(raw) {
  const cmd = raw.trim().toLowerCase();
  if (cmd === "" ) return null;
  if (cmd === "help") return TERMINAL_HELP;
  if (cmd === "whoami")
    return [
      "Ruthwik Thotapelli — Full Stack Developer, Cloud & DevOps.",
      "B.Tech IT, Lovely Professional University.",
      "Builds systems that are secured, automated, and monitored — not demos.",
    ];
  if (cmd === "skills")
    return STACK.flatMap((s) => [`${s.group}:`, "  " + s.items.join(", ")]);
  if (cmd === "projects")
    return PROJECTS.map((p) => `${p.tag}  ${p.name} — ${p.role} [${p.status}]`);
  if (cmd === "experience")
    return EXPERIENCE.flatMap((e) => [`${e.company} — ${e.role}`]);
  if (cmd === "contact")
    return [
      "email:    ruthwikthotapelli8@gmail.com",
      "linkedin: linkedin.com/in/ruthwik-thotapelli",
      "github:   github.com/ruthwik-thotapelli",
    ];
  if (cmd === "sudo hire" || cmd === "sudo hire me")
    return [
      "[sudo] password for recruiter: ********",
      "Permission granted.",
      "Deploying Ruthwik to your team... this may take one interview.",
    ];
  if (cmd === "clear") return "CLEAR";
  return [`command not found: ${raw}`, "type 'help' for available commands"];
}

export default function Portfolio() {
  const [bootIndex, setBootIndex] = useState(0);
  const [bootDone, setBootDone] = useState(false);
  const [history, setHistory] = useState([
    { type: "out", text: "Type 'help' to see what this thing can do." },
  ]);
  const [input, setInput] = useState("");
  const scrollRef = useRef(null);

  useEffect(() => {
    if (bootIndex >= BOOT_LINES.length) {
      setBootDone(true);
      return;
    }
    const t = setTimeout(() => setBootIndex((i) => i + 1), bootIndex === 0 ? 300 : 380);
    return () => clearTimeout(t);
  }, [bootIndex]);

  useEffect(() => {
    if (scrollRef.current) scrollRef.current.scrollTop = scrollRef.current.scrollHeight;
  }, [history]);

  function handleSubmit(e) {
    e.preventDefault();
    if (!input.trim()) return;
    const result = runCommand(input);
    if (result === "CLEAR") {
      setHistory([]);
    } else {
      setHistory((h) => [
        ...h,
        { type: "in", text: input },
        ...(result ? result.map((line) => ({ type: "out", text: line })) : []),
      ]);
    }
    setInput("");
  }

  return (
    <div className="min-h-screen w-full bg-[#0A0E15] text-[#DCE4EE] font-sans">
      <div
        className="fixed inset-0 pointer-events-none opacity-[0.05]"
        style={{
          backgroundImage:
            "linear-gradient(#2DD4BF 1px, transparent 1px), linear-gradient(90deg, #2DD4BF 1px, transparent 1px)",
          backgroundSize: "42px 42px",
        }}
      />

      {/* NAV */}
      <nav className="relative z-10 border-b border-white/[0.06] backdrop-blur-sm sticky top-0 bg-[#0A0E15]/80">
        <div className="max-w-5xl mx-auto px-6 py-4 flex items-center justify-between">
          <div className="flex items-center gap-2 font-mono text-sm text-[#2DD4BF]">
            <Terminal size={16} />
            <span>ruthwik@portfolio</span>
            <span className="text-[#8592A6]">:~$</span>
          </div>
          <div className="hidden sm:flex items-center gap-2 text-xs font-mono text-[#8592A6]">
            <Circle size={8} className="fill-[#2DD4BF] text-[#2DD4BF]" />
            AVAILABLE FOR HIRE
          </div>
        </div>
      </nav>

      {/* HERO */}
      <section className="relative z-10 max-w-5xl mx-auto px-6 pt-16 pb-20">
        <div className="rounded-lg border border-white/[0.08] bg-[#0D1219] shadow-2xl shadow-black/40 overflow-hidden">
          <div className="flex items-center gap-1.5 px-4 py-3 border-b border-white/[0.06] bg-white/[0.02]">
            <div className="w-2.5 h-2.5 rounded-full bg-[#FF5F56]" />
            <div className="w-2.5 h-2.5 rounded-full bg-[#FFBD2E]" />
            <div className="w-2.5 h-2.5 rounded-full bg-[#27C93F]" />
            <span className="ml-3 text-xs font-mono text-[#8592A6]">bash — 80x24</span>
          </div>
          <div className="p-6 font-mono text-sm leading-relaxed min-h-[220px]">
            {BOOT_LINES.slice(0, bootIndex).map((line, i) => (
              <div key={i} className="text-[#8592A6]">
                {line.startsWith("$") ? <span className="text-[#2DD4BF]">{line}</span> : line}
              </div>
            ))}
            {bootDone && (
              <div className="mt-4">
                <h1 className="text-3xl sm:text-4xl font-bold text-[#F3F6FA] tracking-tight">
                  Ruthwik Thotapelli
                </h1>
                <p className="mt-2 text-[#2DD4BF]">
                  Full Stack Developer · Cloud &amp; DevOps Engineer
                </p>
                <p className="mt-4 text-[#8592A6] max-w-xl text-[13px] leading-relaxed">
                  I build systems the way they're meant to be built for production —
                  secured with proper access control, deployed through automated
                  pipelines, monitored once they're live. No shortcuts, no tutorial-grade demos.
                </p>
                <div className="mt-6 flex flex-wrap gap-3">
                  <a href="https://portfolio-ten-henna-l388c8l4pw.vercel.app/" target="_blank" rel="noreferrer"
                    className="inline-flex items-center gap-1.5 px-4 py-2 rounded-md bg-[#2DD4BF] text-[#0A0E15] text-xs font-semibold hover:bg-[#5EEAD4] transition-colors">
                    View Portfolio <ArrowUpRight size={14} />
                  </a>
                  <a href="mailto:ruthwikthotapelli8@gmail.com"
                    className="inline-flex items-center gap-1.5 px-4 py-2 rounded-md border border-white/15 text-[#DCE4EE] text-xs font-semibold hover:border-[#2DD4BF]/60 hover:text-[#2DD4BF] transition-colors">
                    <Mail size={14} /> Email Me
                  </a>
                  <a href="https://linkedin.com/in/ruthwik-thotapelli" target="_blank" rel="noreferrer"
                    className="inline-flex items-center gap-1.5 px-4 py-2 rounded-md border border-white/15 text-[#DCE4EE] text-xs font-semibold hover:border-[#2DD4BF]/60 hover:text-[#2DD4BF] transition-colors">
                    <Linkedin size={14} /> LinkedIn
                  </a>
                  <a href="https://github.com/ruthwik-thotapelli" target="_blank" rel="noreferrer"
                    className="inline-flex items-center gap-1.5 px-4 py-2 rounded-md border border-white/15 text-[#DCE4EE] text-xs font-semibold hover:border-[#2DD4BF]/60 hover:text-[#2DD4BF] transition-colors">
                    <Github size={14} /> GitHub
                  </a>
                </div>
              </div>
            )}
          </div>
        </div>

        {/* STATS */}
        <div className="grid grid-cols-2 sm:grid-cols-4 gap-3 mt-6">
          {STATS.map((s) => (
            <div key={s.label} className="rounded-lg border border-white/[0.08] bg-[#0D1219] px-4 py-4">
              <div className="font-mono text-2xl font-bold text-[#2DD4BF]">{s.value}</div>
              <div className="text-[11px] text-[#8592A6] mt-1 leading-snug">{s.label}</div>
            </div>
          ))}
        </div>
      </section>

      {/* INTERACTIVE TERMINAL */}
      <section className="relative z-10 max-w-5xl mx-auto px-6 pb-20">
        <div className="flex items-center gap-2 mb-3">
          <span className="text-[11px] font-mono tracking-widest text-[#8592A6]">TRY IT — LIVE TERMINAL</span>
          <div className="h-px flex-1 bg-white/[0.08]" />
        </div>
        <div className="rounded-lg border border-white/[0.08] bg-[#0D1219] overflow-hidden">
          <div ref={scrollRef} className="p-4 font-mono text-[13px] h-56 overflow-y-auto space-y-1">
            {history.map((h, i) => (
              <div key={i} className={h.type === "in" ? "text-[#F3F6FA]" : "text-[#8592A6]"}>
                {h.type === "in" ? <span className="text-[#2DD4BF] mr-1">$</span> : null}
                {h.text}
              </div>
            ))}
          </div>
          <form onSubmit={handleSubmit} className="flex items-center gap-2 border-t border-white/[0.06] px-4 py-3">
            <span className="text-[#2DD4BF] font-mono text-sm">$</span>
            <input
              value={input}
              onChange={(e) => setInput(e.target.value)}
              placeholder="type 'help'"
              className="flex-1 bg-transparent outline-none font-mono text-sm text-[#F3F6FA] placeholder:text-[#4A5568]"
              autoComplete="off"
              spellCheck={false}
            />
          </form>
        </div>
      </section>

      {/* STACK */}
      <section className="relative z-10 max-w-5xl mx-auto px-6 pb-20">
        <div className="flex items-center gap-2 mb-6">
          <span className="text-[11px] font-mono tracking-widest text-[#8592A6]">STACK</span>
          <div className="h-px flex-1 bg-white/[0.08]" />
        </div>
        <div className="grid sm:grid-cols-2 lg:grid-cols-3 gap-4">
          {STACK.map((s) => (
            <div key={s.group} className="rounded-lg border border-white/[0.08] bg-[#0D1219] p-5">
              <div className="flex items-center gap-2 text-[#2DD4BF] mb-3">
                <s.icon size={16} />
                <span className="text-xs font-mono tracking-wide">{s.group.toUpperCase()}</span>
              </div>
              <div className="flex flex-wrap gap-2">
                {s.items.map((it) => (
                  <span key={it} className="text-[11px] px-2 py-1 rounded border border-white/[0.08] text-[#B8C2D0] bg-white/[0.02]">
                    {it}
                  </span>
                ))}
              </div>
            </div>
          ))}
        </div>
      </section>

      {/* PROJECTS */}
      <section className="relative z-10 max-w-5xl mx-auto px-6 pb-20">
        <div className="flex items-center gap-2 mb-6">
          <span className="text-[11px] font-mono tracking-widest text-[#8592A6]">DEPLOYED PROJECTS</span>
          <div className="h-px flex-1 bg-white/[0.08]" />
        </div>
        <div className="space-y-4">
          {PROJECTS.map((p) => (
            <div key={p.id} className="rounded-lg border border-white/[0.08] bg-[#0D1219] p-6 hover:border-[#2DD4BF]/30 transition-colors">
              <div className="flex items-start justify-between flex-wrap gap-3">
                <div>
                  <div className="text-[10px] font-mono text-[#8592A6] tracking-widest mb-1">{p.tag}</div>
                  <h3 className="text-lg font-bold text-[#F3F6FA]">{p.name}</h3>
                  <p className="text-sm text-[#2DD4BF] mt-0.5">{p.role}</p>
                </div>
                <span className="inline-flex items-center gap-1.5 text-[10px] font-mono px-2 py-1 rounded-full border border-[#2DD4BF]/30 text-[#2DD4BF]">
                  <CheckCircle2 size={12} /> {p.status}
                </span>
              </div>

              <p className="text-sm text-[#8592A6] mt-4">
                <span className="text-[#B8C2D0]">Problem: </span>{p.problem}
              </p>
              <p className="text-sm text-[#8592A6] mt-1">
                <span className="text-[#B8C2D0]">Solution: </span>{p.solution}
              </p>

              <ul className="mt-4 space-y-1.5">
                {p.metrics.map((m) => (
                  <li key={m} className="flex items-start gap-2 text-[13px] text-[#B8C2D0]">
                    <ChevronRight size={14} className="text-[#2DD4BF] mt-0.5 shrink-0" />
                    {m}
                  </li>
                ))}
              </ul>

              <div className="flex items-center justify-between mt-5 pt-4 border-t border-white/[0.06]">
                <div className="flex flex-wrap gap-2">
                  {p.stack.map((s) => (
                    <span key={s} className="text-[10px] font-mono px-2 py-0.5 rounded bg-white/[0.04] text-[#8592A6]">
                      {s}
                    </span>
                  ))}
                </div>
                {p.link && (
                  <a href={p.link} target="_blank" rel="noreferrer"
                    className="inline-flex items-center gap-1 text-xs font-semibold text-[#2DD4BF] hover:text-[#5EEAD4] shrink-0">
                    Live demo <ExternalLink size={12} />
                  </a>
                )}
              </div>
            </div>
          ))}
        </div>
      </section>

      {/* EXPERIENCE */}
      <section className="relative z-10 max-w-5xl mx-auto px-6 pb-20">
        <div className="flex items-center gap-2 mb-6">
          <span className="text-[11px] font-mono tracking-widest text-[#8592A6]">INDUSTRY SIMULATIONS</span>
          <div className="h-px flex-1 bg-white/[0.08]" />
        </div>
        <div className="grid sm:grid-cols-2 gap-4">
          {EXPERIENCE.map((e) => (
            <div key={e.company} className="rounded-lg border border-white/[0.08] bg-[#0D1219] p-6">
              <div className="flex items-center gap-2 text-[#F3F6FA] font-bold">
                <Briefcase size={15} className="text-[#2DD4BF]" />
                {e.company}
              </div>
              <p className="text-xs text-[#8592A6] mt-1 mb-3">{e.role}</p>
              <ul className="space-y-1.5">
                {e.points.map((pt) => (
                  <li key={pt} className="flex items-start gap-2 text-[13px] text-[#B8C2D0]">
                    <ChevronRight size={14} className="text-[#2DD4BF] mt-0.5 shrink-0" />
                    {pt}
                  </li>
                ))}
              </ul>
            </div>
          ))}
        </div>
      </section>

      {/* ACHIEVEMENTS */}
      <section className="relative z-10 max-w-5xl mx-auto px-6 pb-20">
        <div className="flex items-center gap-2 mb-6">
          <span className="text-[11px] font-mono tracking-widest text-[#8592A6]">ACHIEVEMENTS</span>
          <div className="h-px flex-1 bg-white/[0.08]" />
        </div>
        <div className="grid sm:grid-cols-2 gap-3">
          {[
            "98.93 percentile on the Hitbullseye Aptitude Test",
            "200+ DSA problems solved in C++",
            "Designed and deployed a complete AWS CI/CD pipeline from scratch",
            "Shipped 3 full-stack production applications with live demos",
          ].map((a) => (
            <div key={a} className="flex items-center gap-3 rounded-lg border border-white/[0.08] bg-[#0D1219] px-4 py-3">
              <Award size={16} className="text-[#2DD4BF] shrink-0" />
              <span className="text-[13px] text-[#B8C2D0]">{a}</span>
            </div>
          ))}
        </div>
      </section>

      {/* FOOTER */}
      <footer className="relative z-10 border-t border-white/[0.06]">
        <div className="max-w-5xl mx-auto px-6 py-12 text-center">
          <p className="text-[#8592A6] text-sm">
            Actively open to Software Engineering roles and internships.
          </p>
          <h2 className="text-2xl font-bold text-[#F3F6FA] mt-2">Let's build something.</h2>
          <div className="flex items-center justify-center gap-3 mt-6 flex-wrap">
            <a href="mailto:ruthwikthotapelli8@gmail.com"
              className="inline-flex items-center gap-1.5 px-4 py-2 rounded-md bg-[#2DD4BF] text-[#0A0E15] text-xs font-semibold hover:bg-[#5EEAD4] transition-colors">
              <Mail size={14} /> ruthwikthotapelli8@gmail.com
            </a>
            <a href="https://linkedin.com/in/ruthwik-thotapelli" target="_blank" rel="noreferrer"
              className="inline-flex items-center gap-1.5 px-4 py-2 rounded-md border border-white/15 text-[#DCE4EE] text-xs font-semibold hover:border-[#2DD4BF]/60 hover:text-[#2DD4BF] transition-colors">
              <Linkedin size={14} /> LinkedIn
            </a>
            <a href="https://github.com/ruthwik-thotapelli" target="_blank" rel="noreferrer"
              className="inline-flex items-center gap-1.5 px-4 py-2 rounded-md border border-white/15 text-[#DCE4EE] text-xs font-semibold hover:border-[#2DD4BF]/60 hover:text-[#2DD4BF] transition-colors">
              <Github size={14} /> GitHub
            </a>
          </div>
          <p className="text-[10px] font-mono text-[#4A5568] mt-8">$ echo "thanks for scrolling this far"</p>
        </div>
      </footer>
    </div>
  );
}
