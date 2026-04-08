import { useState, useEffect } from "react";

function Crystal({ size = 24, opacity = 1, rotate = 0 }) {
  return (
    <svg width={size} height={size} viewBox="0 0 24 24" fill="none"
      style={{ opacity, transform: `rotate(${rotate}deg)`, display:"block" }}>
      <line x1="12" y1="2" x2="12" y2="22" stroke="#a8d8f0" strokeWidth="1.5" strokeLinecap="round"/>
      <line x1="2" y1="12" x2="22" y2="12" stroke="#a8d8f0" strokeWidth="1.5" strokeLinecap="round"/>
      <line x1="4.93" y1="4.93" x2="19.07" y2="19.07" stroke="#a8d8f0" strokeWidth="1.5" strokeLinecap="round"/>
      <line x1="19.07" y1="4.93" x2="4.93" y2="19.07" stroke="#a8d8f0" strokeWidth="1.5" strokeLinecap="round"/>
      <circle cx="12" cy="2"  r="1.5" fill="#a8d8f0"/>
      <circle cx="12" cy="22" r="1.5" fill="#a8d8f0"/>
      <circle cx="2"  cy="12" r="1.5" fill="#a8d8f0"/>
      <circle cx="22" cy="12" r="1.5" fill="#a8d8f0"/>
      <circle cx="4.93"  cy="4.93"  r="1.5" fill="#a8d8f0"/>
      <circle cx="19.07" cy="19.07" r="1.5" fill="#a8d8f0"/>
      <circle cx="19.07" cy="4.93"  r="1.5" fill="#a8d8f0"/>
      <circle cx="4.93"  cy="19.07" r="1.5" fill="#a8d8f0"/>
      <circle cx="12" cy="12" r="2.5" fill="#c8ecff"/>
    </svg>
  );
}

function Spinner() {
  return (
    <span style={{
      display:"inline-block", width:13, height:13,
      border:"2px solid #a8d8f0", borderTopColor:"transparent",
      borderRadius:"50%", animation:"spin 0.7s linear infinite",
      verticalAlign:"middle", marginRight:6
    }}/>
  );
}

function formatPostcode(v) {
  v = v.toUpperCase().replace(/\s/g, "");
  if (v.length > 4) v = v.slice(0, 4) + " " + v.slice(4, 6);
  return v;
}
function isValidPostcode(p) {
  return /^\d{4}\s?[A-Z]{2}$/.test(p.toUpperCase().trim());
}

// Extract JSON from any text blob
function extractJSON(text) {
  const cleaned = text.replace(/```json|```/g, "");
  // Try strict object with known keys
  const patterns = [
    /\{\s*"city"[\s\S]*?"frost"\s*:\s*(true|false)\s*\}/,
    /\{\s*"temp"\s*:[\s\S]*?"frost"\s*:\s*(true|false)\s*\}/,
    /\{[\s\S]*?"minTemp"[\s\S]*?\}/,
    /\{[\s\S]*?\}/
  ];
  for (const re of patterns) {
    const m = cleaned.match(re);
    if (m) {
      try { return JSON.parse(m[0]); } catch {}
    }
  }
  return null;
}

// Call Anthropic API — with or without web_search tool
async function callAPI(postcode, useWebSearch) {
  const today = new Date().toLocaleDateString("nl-NL", {
    weekday:"long", day:"numeric", month:"long", year:"numeric"
  });

  const body = {
    model: "claude-sonnet-4-20250514",
    max_tokens: 800,
    messages: [{
      role: "user",
      content: useWebSearch
        ? `Vandaag is het ${today}. Zoek het actuele weer op voor Nederlandse postcode ${postcode}. ` +
          `Geef ALLEEN onderstaand JSON-object terug, geen andere tekst:\n` +
          `{"city":"naam","temp":0,"minTemp":0,"description":"kort","frost":false}`
        : `Vandaag is het ${today}. Geef een realistische weerschatting voor Nederlandse postcode ${postcode} ` +
          `op basis van het seizoen en typisch Nederlands weer. ` +
          `Geef ALLEEN onderstaand JSON-object terug, absoluut geen andere tekst:\n` +
          `{"city":"naam","temp":0,"minTemp":0,"description":"kort","frost":false,"estimated":true}`
    }]
  };

  if (useWebSearch) {
    body.tools = [{ type: "web_search_20250305", name: "web_search" }];
  }

  const res = await fetch("https://api.anthropic.com/v1/messages", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(body)
  });

  const rawText = await res.text();

  if (!res.ok) {
    throw new Error(`HTTP ${res.status}: ${rawText.slice(0, 300)}`);
  }

  let data;
  try { data = JSON.parse(rawText); }
  catch { throw new Error(`Respons geen JSON: ${rawText.slice(0, 200)}`); }

  // Collect all text from all content blocks
  const allText = (data.content || [])
    .map(b => {
      if (b.type === "text") return b.text || "";
      if (b.type === "tool_result" && Array.isArray(b.content))
        return b.content.map(c => c.text || "").join(" ");
      return "";
    })
    .join("\n");

  if (!allText.trim()) {
    const types = (data.content || []).map(b => b.type);
    throw new Error(`Lege tekst. Content types: [${types.join(",")}]. Raw: ${rawText.slice(0,200)}`);
  }

  const parsed = extractJSON(allText);
  if (!parsed) {
    throw new Error(`Geen JSON in: "${allText.slice(0, 300)}"`);
  }
  if (typeof parsed.temp !== "number" || typeof parsed.minTemp !== "number") {
    throw new Error(`Onvolledige velden: ${JSON.stringify(parsed)}`);
  }

  return {
    city:        parsed.city        || postcode,
    temp:        Math.round(parsed.temp),
    minTemp:     Math.round(parsed.minTemp),
    description: parsed.description || "Onbekend",
    frost:       parsed.minTemp < 2,
    estimated:   !!parsed.estimated
  };
}

async function fetchWeather(postcode) {
  const errors = [];

  // Try 1: with web_search
  try {
    return await callAPI(postcode, true);
  } catch(e) { errors.push("(met zoeken) " + e.message); }

  // Try 2: without web_search
  try {
    return await callAPI(postcode, false);
  } catch(e) { errors.push("(zonder zoeken) " + e.message); }

  throw new Error(errors.join(" || "));
}

// ── Component ─────────────────────────────────────────────────────────────────
export default function IjsAlert() {
  const [postcode,     setPostcode]     = useState("");
  const [notifEnabled, setNotifEnabled] = useState(false);
  const [notifTime,    setNotifTime]    = useState("07:30");
  const [status,       setStatus]       = useState(null);
  const [weather,      setWeather]      = useState(null);
  const [loading,      setLoading]      = useState(false);
  const [debugMsg,     setDebugMsg]     = useState("");
  const [showDebug,    setShowDebug]    = useState(false);

  const [crystals] = useState(() =>
    Array.from({length:8}, (_,i) => ({
      id:i, top:`${5+Math.random()*88}%`, left:`${2+Math.random()*94}%`,
      size:12+Math.random()*18, opacity:0.06+Math.random()*0.12,
      rotate:Math.random()*45, delay:Math.random()*4
    }))
  );

  useEffect(() => {
    try {
      const d = JSON.parse(localStorage.getItem("ijsAlert") || "{}");
      if (d.postcode)             setPostcode(d.postcode);
      if (d.notificationTime)     setNotifTime(d.notificationTime);
      if (d.notificationsEnabled) setNotifEnabled(true);
      if (d.weather)              setWeather(d.weather);
    } catch {}
  }, []);

  async function doFetch(pc) {
    if (!isValidPostcode(pc)) {
      setStatus({ msg:"Ongeldige postcode (bijv. 1234 AB)", type:"err" });
      return;
    }
    setLoading(true);
    setStatus({ msg:"Weer ophalen…", type:"info" });
    setWeather(null);
    setDebugMsg("");
    setShowDebug(false);

    try {
      const result = await fetchWeather(pc.toUpperCase().trim());
      setWeather(result);
      const suffix = result.estimated ? " (schatting o.b.v. seizoen)" : "";
      if (result.minTemp < 0)
        setStatus({ msg:`⚠️ Vorst verwacht! Nacht min: ${result.minTemp}°C${suffix}`, type:"warn" });
      else if (result.minTemp < 2)
        setStatus({ msg:`⚠️ Bijna vriespunt (${result.minTemp}°C)${suffix}`, type:"warn" });
      else
        setStatus({ msg:`✓ Geen ijs verwacht (min ${result.minTemp}°C)${suffix}`, type:"ok" });
    } catch(e) {
      setStatus({ msg:"❌ Kon weerdata niet ophalen", type:"err" });
      setDebugMsg(e.message);
    } finally {
      setLoading(false);
    }
  }

  async function handleSave() {
    if (!postcode || !isValidPostcode(postcode)) {
      setStatus({ msg:"Voer een geldige postcode in", type:"err" });
      return;
    }
    await doFetch(postcode);
    localStorage.setItem("ijsAlert", JSON.stringify({
      postcode, notificationsEnabled:notifEnabled,
      notificationTime:notifTime, weather,
      savedAt:new Date().toISOString()
    }));
    if (notifEnabled && "Notification" in window) {
      const perm = Notification.permission === "default"
        ? await Notification.requestPermission()
        : Notification.permission;
      if (perm === "granted")
        setStatus({ msg:`✓ Opgeslagen! Melding elke dag om ${notifTime}`, type:"ok" });
    }
  }

  const sc = {
    ok:   {bg:"#0d2e1a",border:"#1a5c30",text:"#4ade80"},
    warn: {bg:"#2e200a",border:"#5c3d0d",text:"#fbbf24"},
    err:  {bg:"#2e0a0a",border:"#5c1a1a",text:"#f87171"},
    info: {bg:"#0a1a2e",border:"#1a3a5c",text:"#a8d8f0"},
  };

  const tempColor = !weather ? "#a8d8f0"
    : weather.temp <= 0  ? "#7dd3fc"
    : weather.temp <= 5  ? "#a8d8f0"
    : weather.temp <= 15 ? "#86efac"
    : "#fca5a5";

  return (
    <div style={{
      minHeight:"100vh",
      background:"linear-gradient(160deg,#040b18 0%,#071222 40%,#050e1c 100%)",
      display:"flex", alignItems:"center", justifyContent:"center",
      padding:"20px", fontFamily:"'DM Sans',system-ui,sans-serif",
      position:"relative", overflow:"hidden"
    }}>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=DM+Serif+Display&family=DM+Sans:wght@300;400;500;600&display=swap');
        @keyframes spin{to{transform:rotate(360deg)}}
        @keyframes floatC{0%,100%{transform:translateY(0)}50%{transform:translateY(-14px)}}
        @keyframes fadeIn{from{opacity:0;transform:translateY(14px)}to{opacity:1;transform:translateY(0)}}
        .card{animation:fadeIn .7s cubic-bezier(.16,1,.3,1) both}
        .fc{animation:floatC var(--d,5s) ease-in-out var(--dl,0s) infinite}
        .sb:hover:not(:disabled){transform:translateY(-1px);box-shadow:0 12px 28px rgba(100,200,255,.25)}
        .sb:active:not(:disabled){transform:translateY(0)}
        .sb:disabled{opacity:.45;cursor:not-allowed}
        input:focus{outline:none}
        ::placeholder{color:#2a4a6a!important}
        input[type=time]::-webkit-calendar-picker-indicator{filter:invert(.6)}
      `}</style>

      {crystals.map(c=>(
        <div key={c.id} className="fc" style={{
          position:"absolute",top:c.top,left:c.left,pointerEvents:"none",
          "--d":`${3.5+c.delay}s`,"--dl":`${c.delay}s`
        }}>
          <Crystal size={c.size} opacity={c.opacity} rotate={c.rotate}/>
        </div>
      ))}

      <div style={{
        position:"absolute",top:"20%",left:"30%",width:400,height:400,borderRadius:"50%",
        pointerEvents:"none",
        background:"radial-gradient(circle,rgba(100,180,255,.04) 0%,transparent 70%)"
      }}/>

      <div className="card" style={{
        background:"linear-gradient(145deg,rgba(12,24,46,.97) 0%,rgba(8,18,36,.99) 100%)",
        border:"1px solid rgba(168,216,240,.12)",borderRadius:28,
        padding:"36px 28px 32px",maxWidth:360,width:"100%",
        boxShadow:"0 32px 80px rgba(0,0,0,.7),inset 0 1px 0 rgba(168,216,240,.08)"
      }}>

        <div style={{textAlign:"center",marginBottom:32}}>
          <div style={{marginBottom:14,display:"flex",justifyContent:"center"}}>
            <Crystal size={50} opacity={0.9}/>
          </div>
          <h1 style={{
            fontFamily:"'DM Serif Display',serif",fontSize:32,fontWeight:400,
            color:"#e4f4ff",margin:"0 0 6px",textShadow:"0 0 30px rgba(168,216,240,.3)"
          }}>Ijs Alert</h1>
          <p style={{fontSize:12,color:"#3a6a8a",letterSpacing:".08em",textTransform:"uppercase"}}>
            Dagelijkse vorstmelding
          </p>
        </div>

        <div style={{marginBottom:20}}>
          <label style={{
            display:"block",fontSize:11,fontWeight:600,color:"#3a6a8a",
            letterSpacing:".1em",textTransform:"uppercase",marginBottom:8
          }}>Postcode</label>
          <div style={{display:"flex",gap:8}}>
            <input
              type="text" value={postcode} placeholder="1234 AB" maxLength={7}
              onChange={e=>setPostcode(formatPostcode(e.target.value))}
              onKeyDown={e=>{if(e.key==="Enter")doFetch(postcode);}}
              style={{
                flex:1,padding:"13px 16px",
                background:"rgba(168,216,240,.05)",
                border:"1.5px solid rgba(168,216,240,.14)",
                borderRadius:12,fontSize:16,color:"#c8e8ff",
                fontFamily:"inherit",caretColor:"#a8d8f0"
              }}
            />
            <button onClick={()=>doFetch(postcode)} disabled={loading} style={{
              padding:"13px 18px",
              background:"rgba(168,216,240,.08)",
              border:"1.5px solid rgba(168,216,240,.14)",
              borderRadius:12,color:"#a8d8f0",cursor:"pointer",
              fontSize:18,flexShrink:0,opacity:loading?0.5:1
            }}>
              {loading?<Spinner/>:"→"}
            </button>
          </div>
        </div>

        {weather && (
          <div style={{
            background:"rgba(168,216,240,.04)",border:"1px solid rgba(168,216,240,.1)",
            borderRadius:14,padding:"18px 20px",marginBottom:20,
            textAlign:"center",animation:"fadeIn .4s ease"
          }}>
            <div style={{fontSize:11,color:"#3a6a8a",letterSpacing:".1em",
              textTransform:"uppercase",marginBottom:8}}>
              {weather.city}{weather.estimated?" · schatting":""}
            </div>
            <div style={{
              fontSize:44,fontFamily:"'DM Serif Display',serif",color:tempColor,
              fontWeight:400,lineHeight:1,textShadow:`0 0 24px ${tempColor}60`,marginBottom:6
            }}>{weather.temp}°</div>
            <div style={{fontSize:13,color:"#5a8aaa",marginBottom:10}}>{weather.description}</div>
            <div style={{
              display:"inline-flex",alignItems:"center",gap:6,
              background:weather.frost?"rgba(251,191,36,.1)":"rgba(74,222,128,.08)",
              border:`1px solid ${weather.frost?"rgba(251,191,36,.2)":"rgba(74,222,128,.15)"}`,
              borderRadius:20,padding:"4px 14px",
              fontSize:12,color:weather.frost?"#fbbf24":"#4ade80"
            }}>
              {weather.frost?"⚠️":"✓"} Nacht min: {weather.minTemp}°C
            </div>
          </div>
        )}

        {status && (()=>{
          const c=sc[status.type]||sc.info;
          return(
            <div style={{
              background:c.bg,border:`1px solid ${c.border}`,borderRadius:12,
              padding:"12px 16px",marginBottom:20,fontSize:13,color:c.text,
              textAlign:"center",fontWeight:500,animation:"fadeIn .3s ease"
            }}>
              {status.type==="info"&&<Spinner/>}{status.msg}
            </div>
          );
        })()}

        {debugMsg && (
          <div style={{marginBottom:16,textAlign:"center"}}>
            <button onClick={()=>setShowDebug(v=>!v)} style={{
              background:"none",border:"none",color:"#2a4060",
              fontSize:11,cursor:"pointer",textDecoration:"underline"
            }}>
              {showDebug?"Verberg":"Toon"} debug info
            </button>
            {showDebug&&(
              <div style={{
                marginTop:8,padding:"10px 12px",
                background:"rgba(0,0,0,.4)",
                border:"1px solid rgba(168,216,240,.08)",
                borderRadius:8,fontSize:10,color:"#4a7a9b",
                textAlign:"left",wordBreak:"break-all",lineHeight:1.5
              }}>{debugMsg}</div>
            )}
          </div>
        )}

        <div style={{
          background:"rgba(168,216,240,.03)",border:"1px solid rgba(168,216,240,.08)",
          borderRadius:16,padding:"18px 20px",marginBottom:24
        }}>
          <div style={{display:"flex",justifyContent:"space-between",alignItems:"center"}}>
            <span style={{fontSize:13,fontWeight:500,color:"#8ab8d4"}}>Ochtend meldingen</span>
            <button onClick={()=>setNotifEnabled(v=>!v)} style={{
              position:"relative",width:48,height:26,
              background:notifEnabled
                ?"linear-gradient(135deg,#1a6abf 0%,#0e4a8a 100%)"
                :"rgba(168,216,240,.1)",
              borderRadius:13,border:"none",cursor:"pointer",flexShrink:0,
              transition:"background .3s"
            }}>
              <span style={{
                position:"absolute",width:20,height:20,
                background:notifEnabled?"#a8d8f0":"#2a4a6a",
                borderRadius:"50%",top:3,left:notifEnabled?25:3,
                transition:"all .25s cubic-bezier(.34,1.56,.64,1)",
                boxShadow:notifEnabled?"0 0 8px rgba(168,216,240,.5)":"none"
              }}/>
            </button>
          </div>
          <div style={{
            display:"flex",justifyContent:"space-between",alignItems:"center",
            marginTop:16,paddingTop:16,borderTop:"1px solid rgba(168,216,240,.07)"
          }}>
            <span style={{fontSize:13,fontWeight:500,color:"#8ab8d4"}}>Meldingstijd</span>
            <input type="time" value={notifTime} onChange={e=>setNotifTime(e.target.value)}
              style={{
                background:"rgba(168,216,240,.08)",
                border:"1.5px solid rgba(168,216,240,.14)",
                borderRadius:8,padding:"6px 10px",
                fontSize:14,fontWeight:600,color:"#a8d8f0",
                fontFamily:"inherit",width:88,textAlign:"center",colorScheme:"dark"
              }}
            />
          </div>
        </div>

        <button className="sb" onClick={handleSave} disabled={loading} style={{
          width:"100%",padding:"15px",
          background:"linear-gradient(135deg,#1a5a9e 0%,#0d3d7a 100%)",
          border:"1px solid rgba(168,216,240,.2)",borderRadius:14,
          fontSize:14,fontWeight:600,color:"#c8e8ff",cursor:"pointer",
          letterSpacing:".06em",textTransform:"uppercase",
          transition:"all .2s",boxShadow:"0 6px 20px rgba(10,50,120,.5)"
        }}>
          {loading?<><Spinner/>Ophalen…</>:"Instellingen opslaan"}
        </button>

        <p style={{fontSize:11,color:"#253a50",textAlign:"center",marginTop:14,lineHeight:1.5}}>
          Melding bij nachttemperatuur onder 2°C
        </p>
      </div>
    </div>
  );
}
