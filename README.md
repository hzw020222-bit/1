import { useState, useEffect } from "react";

const STAGES = [
  { name: "Egg", minXP: 0, color: "#a8e6cf", size: 60 },
  { name: "Hatchling", minXP: 50, color: "#88d8b0", size: 80 },
  { name: "Coder Pup", minXP: 150, color: "#56c596", size: 100 },
  { name: "Dev Sprite", minXP: 300, color: "#38b880", size: 120 },
  { name: "Code Wizard", minXP: 500, color: "#1a9e6a", size: 140 },
];

const PET_PIXELS = {
  Egg: [
    "  XXX  ",
    " XXXXX ",
    "XXXXXXX",
    "XXXXXXX",
    "XXXXXXX",
    " XXXXX ",
    "  XXX  ",
  ],
  Hatchling: [
    "  ooo  ",
    " oXXXo ",
    "oXoXoXo",
    "oXXXXXo",
    " oXXXo ",
    "  ooo  ",
    " o   o ",
  ],
  "Coder Pup": [
    " o   o ",
    "oXXXXXo",
    "oX o Xo",
    "oXXXXXo",
    " oXXXo ",
    "o o o o",
    "       ",
  ],
  "Dev Sprite": [
    "o     o",
    "oXXXXXo",
    "oXoooXo",
    "oXXXXXo",
    "oXo Xo ",
    " oXXo  ",
    "o o o  ",
  ],
  "Code Wizard": [
    "  oXo  ",
    " oXXXo ",
    "oXXXXXo",
    "oX o Xo",
    "oXXXXXo",
    " oXXXo ",
    "o o o o",
  ],
};

function PixelPet({ stage, animated }) {
  const pixels = PET_PIXELS[stage.name] || PET_PIXELS["Egg"];
  const px = 12;
  return (
    <div style={{ display: "inline-block", imageRendering: "pixelated" }}>
      {pixels.map((row, y) => (
        <div key={y} style={{ display: "flex" }}>
          {row.split("").map((cell, x) => (
            <div
              key={x}
              style={{
                width: px,
                height: px,
                backgroundColor:
                  cell === "X"
                    ? stage.color
                    : cell === "o"
                    ? "#2d2d2d"
                    : "transparent",
                transition: animated ? "background-color 0.3s" : "none",
              }}
            />
          ))}
        </div>
      ))}
    </div>
  );
}

function XPBar({ current, next, color }) {
  const pct = next ? Math.min(100, ((current - 0) / next) * 100) : 100;
  return (
    <div
      style={{
        background: "#1a1a2e",
        borderRadius: 4,
        height: 12,
        width: "100%",
        overflow: "hidden",
        border: "1px solid #333",
      }}
    >
      <div
        style={{
          width: `${pct}%`,
          height: "100%",
          background: color,
          transition: "width 0.5s ease",
          boxShadow: `0 0 8px ${color}`,
        }}
      />
    </div>
  );
}

export default function App() {
  const [streak, setStreak] = useState(3);
  const [hours, setHours] = useState(2);
  const [commits, setCommits] = useState(5);
  const [projects, setProjects] = useState(1);
  const [animated, setAnimated] = useState(false);
  const [showLevelUp, setShowLevelUp] = useState(false);

  const xp = streak * 10 + hours * 8 + commits * 5 + projects * 20;
  const stageIndex = STAGES.reduce(
    (acc, s, i) => (xp >= s.minXP ? i : acc),
    0
  );
  const stage = STAGES[stageIndex];
  const nextStage = STAGES[stageIndex + 1];

  const prevXP =
    stageIndex > 0 ? STAGES[stageIndex - 1]?.minXP ?? 0 : 0;
  const xpInStage = xp - stage.minXP;
  const xpNeeded = nextStage ? nextStage.minXP - stage.minXP : 1;

  useEffect(() => {
    setAnimated(true);
    const t = setTimeout(() => setAnimated(false), 600);
    return () => clearTimeout(t);
  }, [streak, hours, commits, projects]);

  const mood =
    xp > 400
      ? "✨ Legendary!"
      : xp > 200
      ? "🔥 On fire!"
      : xp > 100
      ? "😊 Happy"
      : xp > 30
      ? "🌱 Growing"
      : "😴 Sleepy";

  const font = "'Press Start 2P', monospace";

  return (
    <div
      style={{
        minHeight: "100vh",
        background: "#0d0d1a",
        display: "flex",
        alignItems: "center",
        justifyContent: "center",
        fontFamily: font,
        padding: 20,
      }}
    >
      <link
        href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap"
        rel="stylesheet"
      />

      <div
        style={{
          width: 360,
          background: "#13132b",
          border: "3px solid #2a2a5a",
          borderRadius: 12,
          padding: 24,
          boxShadow: `0 0 40px #1a1a4a, 0 0 80px #0a0a20`,
        }}
      >
        {/* Header */}
        <div style={{ textAlign: "center", marginBottom: 16 }}>
          <div style={{ color: "#56c596", fontSize: 10, letterSpacing: 2 }}>
            ◈ CODE PET ◈
          </div>
          <div style={{ color: "#fff", fontSize: 7, marginTop: 4, opacity: 0.5 }}>
            v1.0 — grow by coding
          </div>
        </div>

        {/* Pet Display */}
        <div
          style={{
            background: "#0a0a18",
            borderRadius: 8,
            padding: "20px 0",
            textAlign: "center",
            marginBottom: 16,
            border: "1px solid #1a1a3a",
            position: "relative",
            minHeight: 130,
            display: "flex",
            flexDirection: "column",
            alignItems: "center",
            justifyContent: "center",
          }}
        >
          <div
            style={{
              transform: animated ? "scale(1.08)" : "scale(1)",
              transition: "transform 0.3s",
            }}
          >
            <PixelPet stage={stage} animated={animated} />
          </div>
          <div
            style={{
              color: stage.color,
              fontSize: 8,
              marginTop: 10,
              textShadow: `0 0 12px ${stage.color}`,
            }}
          >
            {stage.name}
          </div>
          <div style={{ color: "#888", fontSize: 7, marginTop: 4 }}>
            {mood}
          </div>
        </div>

        {/* XP */}
        <div style={{ marginBottom: 16 }}>
          <div
            style={{
              display: "flex",
              justifyContent: "space-between",
              marginBottom: 6,
              color: "#aaa",
              fontSize: 7,
            }}
          >
            <span>XP: {xp}</span>
            <span>{nextStage ? `Next: ${nextStage.minXP} XP` : "MAX"}</span>
          </div>
          <XPBar
            current={xpInStage}
            next={xpNeeded}
            color={stage.color}
          />
          {nextStage && (
            <div style={{ color: "#555", fontSize: 6, marginTop: 4, textAlign: "right" }}>
              {xpNeeded - xpInStage} XP to {nextStage.name}
            </div>
          )}
        </div>

        {/* Stats */}
        <div style={{ marginBottom: 16 }}>
          <div style={{ color: "#56c596", fontSize: 7, marginBottom: 10 }}>
            ▸ HABITS
          </div>
          {[
            { label: "🔥 Streak", val: streak, set: setStreak, max: 30, xpPer: 10, unit: "days" },
            { label: "⏱ Hours/day", val: hours, set: setHours, max: 12, xpPer: 8, unit: "hrs" },
            { label: "📦 Commits", val: commits, set: setCommits, max: 20, xpPer: 5, unit: "" },
            { label: "🚀 Projects", val: projects, set: setProjects, max: 10, xpPer: 20, unit: "" },
          ].map(({ label, val, set, max, xpPer, unit }) => (
            <div key={label} style={{ marginBottom: 10 }}>
              <div
                style={{
                  display: "flex",
                  justifyContent: "space-between",
                  marginBottom: 4,
                  fontSize: 7,
                  color: "#ccc",
                }}
              >
                <span>{label}</span>
                <span style={{ color: stage.color }}>
                  {val}{unit ? " " + unit : ""} → +{val * xpPer} XP
                </span>
              </div>
              <input
                type="range"
                min={0}
                max={max}
                value={val}
                onChange={(e) => set(Number(e.target.value))}
                style={{
                  width: "100%",
                  accentColor: stage.color,
                  cursor: "pointer",
                }}
              />
            </div>
          ))}
        </div>

        {/* Evolution ladder */}
        <div
          style={{
            background: "#0a0a18",
            borderRadius: 6,
            padding: "10px 12px",
            border: "1px solid #1a1a3a",
          }}
        >
          <div style={{ color: "#56c596", fontSize: 6, marginBottom: 8 }}>
            ▸ EVOLUTION PATH
          </div>
          {STAGES.map((s, i) => (
            <div
              key={s.name}
              style={{
                display: "flex",
                alignItems: "center",
                gap: 8,
                marginBottom: 5,
                opacity: i <= stageIndex ? 1 : 0.3,
              }}
            >
              <div
                style={{
                  width: 8,
                  height: 8,
                  borderRadius: "50%",
                  background: i <= stageIndex ? s.color : "#333",
                  boxShadow: i === stageIndex ? `0 0 8px ${s.color}` : "none",
                  flexShrink: 0,
                }}
              />
              <span style={{ color: i <= stageIndex ? s.color : "#444", fontSize: 7 }}>
                {s.name}
              </span>
              <span style={{ color: "#333", fontSize: 6, marginLeft: "auto" }}>
                {s.minXP} XP
              </span>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}
