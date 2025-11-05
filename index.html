const { useState, useRef, useEffect } = React;
const { jsPDF } = window.jspdf || {};

/* angle helper */
function angleBetween(p1, p2, p3) {
  if (!p1 || !p2 || !p3) return NaN;
  const ax = p1.x - p2.x, ay = p1.y - p2.y;
  const bx = p3.x - p2.x, by = p3.y - p2.y;
  const dot = ax * bx + ay * by;
  const magA = Math.hypot(ax, ay), magB = Math.hypot(bx, by);
  if (!magA || !magB) return NaN;
  let cos = dot / (magA * magB);
  cos = Math.max(-1, Math.min(1, cos));
  return Math.abs((Math.acos(cos) * 180) / Math.PI);
}

/* thresholds and joint mapping */
const EX_THRESH = {
  shoulder: { min: 90, expected: 180, label: "Shoulder (abduction)" },
  elbow: { min: 100, expected: 145, label: "Elbow (flexion)" },
  knee: { min: 10, expected: 0, label: "Knee (extension)" },
  hip: { min: 80, expected: 120, label: "Hip (flexion)" }
};

function FinalROMApp() {
  const videoRef = useRef(null);
  const canvasRef = useRef(null);
  const poseRef = useRef(null);
  const cameraRef = useRef(null);
  const samplesRef = useRef({ left: [], right: [] });

  const [cameraOn, setCameraOn] = useState(false);
  const [running, setRunning] = useState(false);
  const [joint, setJoint] = useState("shoulder");
  const [secondsLeft, setSecondsLeft] = useState(15);
  const [patientName, setPatientName] = useState("");
  const [report, setReport] = useState(null);
  const [statusMsg, setStatusMsg] = useState("");

  useEffect(() => {
    poseRef.current = new window.Pose({
      locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/pose/${file}`
    });
    poseRef.current.setOptions({
      modelComplexity: 1,
      smoothLandmarks: true,
      minDetectionConfidence: 0.5,
      minTrackingConfidence: 0.5
    });
    poseRef.current.onResults(onResults);

    // make sure lucide runs
    try { if (window.lucide && window.lucide.createIcons) window.lucide.createIcons(); } catch(e){}

    return () => { try { if (cameraRef.current) cameraRef.current.stop(); } catch(e){} };
  }, []);

  function onResults(results) {
    const video = videoRef.current;
    const canvas = canvasRef.current;
    if (!canvas || !video) return;
    const ctx = canvas.getContext("2d");
    canvas.width = video.videoWidth || 640;
    canvas.height = video.videoHeight || 480;

    ctx.clearRect(0,0,canvas.width,canvas.height);
    ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

    const lm = results.poseLandmarks || [];
    if (!lm || lm.length === 0) {
      ctx.fillStyle = "rgba(0,0,0,0.5)";
      ctx.fillRect(10,10,220,28);
      ctx.fillStyle = "#fff";
      ctx.font = "14px sans-serif";
      ctx.fillText("No landmarks detected - reposition or improve lighting", 16, 30);
      return;
    }

    // connections to draw (simple)
    const CONNECTIONS = [
      [11,12],[11,13],[13,15],[12,14],[14,16],
      [11,23],[12,24],[23,24],[23,25],[25,27],
      [24,26],[26,28]
    ];
    ctx.strokeStyle = "#3b82f6"; ctx.lineWidth = 2;
    CONNECTIONS.forEach(([a,b])=>{
      if (lm[a] && lm[b]) {
        ctx.beginPath();
        ctx.moveTo(lm[a].x * canvas.width, lm[a].y * canvas.height);
        ctx.lineTo(lm[b].x * canvas.width, lm[b].y * canvas.height);
        ctx.stroke();
      }
    });

    // draw points for visibility
    ctx.fillStyle = "#10b981";
    lm.forEach(p=>{
      ctx.beginPath();
      ctx.arc(p.x * canvas.width, p.y * canvas.height, 4, 0, 2*Math.PI);
      ctx.fill();
    });

    // compute the joint angles (left/right) depending on selected joint
    let leftAngle = NaN, rightAngle = NaN;
    if (joint === "shoulder") {
      leftAngle = angleBetween(lm[23], lm[11], lm[13]);   // hip-shoulder-elbow
      rightAngle = angleBetween(lm[24], lm[12], lm[14]);
    } else if (joint === "elbow") {
      leftAngle = angleBetween(lm[11], lm[13], lm[15]);   // shoulder-elbow-wrist
      rightAngle = angleBetween(lm[12], lm[14], lm[16]);
    } else if (joint === "knee") {
      leftAngle = 180 - angleBetween(lm[23], lm[25], lm[27]); // extension
      rightAngle = 180 - angleBetween(lm[24], lm[26], lm[28]);
    } else if (joint === "hip") {
      leftAngle = angleBetween(lm[11], lm[23], lm[25]);    // shoulder-hip-knee
      rightAngle = angleBetween(lm[12], lm[24], lm[26]);
    }

    // record samples when running
    if (running) {
      if (isFinite(leftAngle)) samplesRef.current.left.push(leftAngle);
      if (isFinite(rightAngle)) samplesRef.current.right.push(rightAngle);
    }

    // overlay readouts
    ctx.fillStyle = "rgba(0,0,0,0.55)";
    ctx.fillRect(10,10,260,80);
    ctx.fillStyle = "#fff"; ctx.font = "14px sans-serif";
    ctx.fillText(`${EX_THRESH[joint].label || joint.toUpperCase()}`, 18, 30);
    ctx.fillText(`L: ${isFinite(leftAngle) ? leftAngle.toFixed(1) + '°' : '-'}`, 18, 52);
    ctx.fillText(`R: ${isFinite(rightAngle) ? rightAngle.toFixed(1) + '°' : '-'}`, 140, 52);
    ctx.fillStyle = "#ffd700"; ctx.font = "12px sans-serif";
    ctx.fillText(`Recording: ${running ? 'YES' : 'NO'}`, 18, 74);
  }

  async function startCamera() {
    setStatusMsg("Starting camera...");
    try {
      const cam = new window.Camera(videoRef.current, {
        onFrame: async () => {
          await poseRef.current.send({ image: videoRef.current });
        },
        width: 640,
        height: 480
      });
      cameraRef.current = cam;
      cam.start();
      setCameraOn(true);
      setStatusMsg("Camera active");
    } catch (err) {
      console.error(err);
      setStatusMsg("Camera failed: allow permissions or use secure origin");
    }
  }

  function stopCamera() {
    try { if (cameraRef.current) cameraRef.current.stop(); } catch(e){}
    setCameraOn(false);
    setRunning(false);
    setStatusMsg("Camera stopped");
  }

  function resetAll() {
    samplesRef.current = { left: [], right: [] };
    setReport(null);
    setSecondsLeft(15);
    setStatusMsg("");
  }

  function statsFrom(arr) {
    const clean = (arr || []).filter(v => isFinite(v));
    if (clean.length === 0) return { count:0, max:NaN, min:NaN, mean:NaN };
    const max = Math.max(...clean), min = Math.min(...clean);
    const mean = clean.reduce((s,x)=>s+x,0)/clean.length;
    return { count: clean.length, max, min, mean };
  }

  function analyzeAndGenerate() {
    const L = statsFrom(samplesRef.current.left);
    const R = statsFrom(samplesRef.current.right);
    const asym = isFinite(L.max) && isFinite(R.max) ? Math.abs(L.max - R.max) : NaN;
    const thr = EX_THRESH[joint];

    const observations = [];
    if (isFinite(L.max) && L.max < thr.min) observations.push(`Left ${thr.label} limited (max ${L.max.toFixed(1)}°, expected ≥ ${thr.min}°).`);
    if (isFinite(R.max) && R.max < thr.min) observations.push(`Right ${thr.label} limited (max ${R.max.toFixed(1)}°, expected ≥ ${thr.min}°).`);
    if (isFinite(asym) && asym > 15) observations.push(`Asymmetry detected (L ${isFinite(L.max)?L.max.toFixed(1)+'°':'-'}, R ${isFinite(R.max)?R.max.toFixed(1)+'°':'-'}) — difference ${asym.toFixed(1)}°.`);
    if (observations.length === 0) observations.push(`ROM within expected limits for ${thr.label}.`);

    const result = {
      patient: patientName || 'Unknown',
      joint,
      durationSec: 15,
      left: L,
      right: R,
      asymmetry: asym,
      observations,
      timestamp: new Date().toISOString()
    };

    setReport(result);
    try { createPdf(result); } catch(e){ console.error("PDF fail", e); }
  }

  function createPdf(data) {
    if (!jsPDF) {
      console.warn("jsPDF not loaded");
      return;
    }
    const doc = new jsPDF({ unit: 'pt', format: 'a4' });
    const pad = 40;
    doc.setFontSize(16);
    doc.text('Physiotherapy ROM Assessment', 210, pad, { align: 'center' });
    doc.setFontSize(10);
    doc.text(`Generated: ${new Date(data.timestamp).toLocaleString()}`, pad, pad + 30);
    doc.text(`Patient: ${data.patient}`, pad, pad + 50);
    doc.text(`Joint: ${data.joint}`, pad, pad + 70);
    doc.text(`Duration: ${data.durationSec} seconds`, pad, pad + 90);

    let y = pad + 120;
    doc.setFontSize(12);
    doc.text('Results', pad, y); y += 18;
    doc.setFontSize(10);
    doc.text(`Left - samples: ${data.left.count}, max: ${isFinite(data.left.max)?data.left.max.toFixed(1)+'°':'-'}, mean: ${isFinite(data.left.mean)?data.left.mean.toFixed(1)+'°':'-'}`, pad, y); y+=16;
    doc.text(`Right - samples: ${data.right.count}, max: ${isFinite(data.right.max)?data.right.max.toFixed(1)+'°':'-'}, mean: ${isFinite(data.right.mean)?data.right.mean.toFixed(1)+'°':'-'}`, pad, y); y+=20;
    doc.text(`Asymmetry (|L-R|): ${isFinite(data.asymmetry)?data.asymmetry.toFixed(1)+'°':'-'}`, pad, y); y+=24;

    doc.text('Clinical observations:', pad, y); y+=18;
    data.observations.forEach(obs=>{
      doc.text(`- ${obs}`, pad+10, y);
      y += 16;
      if (y > 750) { doc.addPage(); y = 40; }
    });

    y += 30;
    doc.text('Assessor: ______________________    Signature: ____________________', pad, y);
    const fname = `${data.patient.replace(/\s+/g,'_') || 'patient'}_${data.joint}_ROM_${(new Date()).toISOString().slice(0,19).replace(/[:T]/g,'-')}.pdf`;
    doc.save(fname);
  }

  async function startAssessment() {
    // ensure camera on
    try {
      if (!cameraOn) await startCamera();
    } catch(e){ /* startCamera handles messages */ }

    // reset samples and report
    samplesRef.current = { left: [], right: [] };
    setReport(null);
    setSecondsLeft(15);
    setRunning(true);
    setStatusMsg("Recording...");

    // countdown
    let t = 15;
    const tick = () => {
      setSecondsLeft(t);
      if (t <= 0) {
        setRunning(false);
        setStatusMsg("Analyzing...");
        try { if (cameraRef.current) cameraRef.current.stop(); } catch(e){}
        setCameraOn(false);
        analyzeAndGenerate();
        setStatusMsg("Assessment complete");
        return;
      }
      t -= 1;
      setTimeout(tick, 1000);
    };
    setTimeout(tick, 0);
  }

  return (
    <div className="min-h-screen p-6 bg-slate-50">
      <div className="max-w-6xl mx-auto bg-white rounded-2xl shadow p-6">
        <header className="flex items-center justify-between mb-4">
          <div className="flex items-center gap-3">
            <i data-lucide="activity" className="w-7 h-7 text-blue-600"></i>
            <h1 className="text-2xl font-semibold">Single-Joint ROM Assessment</h1>
          </div>
          <div className="flex items-center gap-3">
            <input value={patientName} onChange={(e)=>setPatientName(e.target.value)} placeholder="Patient name" className="px-3 py-2 border rounded" />
            <div className="text-sm text-gray-600">Status: <strong>{statusMsg || (cameraOn? "Camera on":"Idle")}</strong></div>
          </div>
        </header>

        <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
          <div className="lg:col-span-2">
            <div className="relative bg-black rounded-lg overflow-hidden aspect-video">
              <video ref={videoRef} className="absolute inset-0 w-full h-full object-cover" playsInline muted />
              <canvas ref={canvasRef} className="absolute inset-0 w-full h-full" />
              {!cameraOn && (
                <div className="absolute inset-0 flex items-center justify-center pointer-events-none">
                  <i data-lucide="camera" className="w-16 h-16 text-gray-400"></i>
                </div>
              )}
            </div>

            <div className="mt-4 flex items-center justify-between">
              <div className="flex items-center gap-2">
                <button onClick={startCamera} disabled={cameraOn} className="px-4 py-2 bg-blue-600 text-white rounded disabled:opacity-60">Start Camera</button>
                <button onClick={stopCamera} className="px-4 py-2 bg-gray-200 rounded">Stop</button>
                <button onClick={resetAll} className="px-4 py-2 bg-white border rounded">Reset</button>
              </div>

              <div className="text-sm">
                Recording time: <strong>{running ? secondsLeft + 's' : '15s'}</strong>
              </div>
            </div>

            <div className="mt-3">
              <div className="h-3 bg-gray-200 rounded">
                <div className="h-3 bg-green-500 rounded" style={{ width: `${((15 - secondsLeft)/15)*100}%` }} />
              </div>
            </div>

            <div className="mt-4">
              {report ? (
                <div className="bg-white border rounded p-4">
                  <h3 className="font-semibold">Assessment Report</h3>
                  <div className="mt-2 text-sm text-gray-700">
                    <div><strong>Patient:</strong> {report.patient}</div>
                    <div><strong>Joint:</strong> {report.joint}</div>
                    <div className="mt-2"><strong>Left - max:</strong> {isFinite(report.left.max)?report.left.max.toFixed(1)+'°':'-'} &nbsp; <strong>mean:</strong> {isFinite(report.left.mean)?report.left.mean.toFixed(1)+'°':'-'}</div>
                    <div><strong>Right - max:</strong> {isFinite(report.right.max)?report.right.max.toFixed(1)+'°':'-'} &nbsp; <strong>mean:</strong> {isFinite(report.right.mean)?report.right.mean.toFixed(1)+'°':'-'}</div>
                    <div className="mt-2"><strong>Asymmetry:</strong> {isFinite(report.asymmetry)?report.asymmetry.toFixed(1)+'°':'-'}</div>

                    <div className="mt-3">
                      <strong>Observations</strong>
                      <ul className="list-disc pl-5 mt-2">
                        {report.observations.map((o,i)=>(<li key={i}>{o}</li>))}
                      </ul>
                    </div>

                    <div className="mt-4">
                      <button onClick={()=>createPdf(report)} className="px-3 py-2 bg-blue-600 text-white rounded">Download PDF</button>
                    </div>
                  </div>
                </div>
              ) : (
                <div className="bg-white border rounded p-3 text-sm text-gray-700">
                  No report yet. Start camera, position patient so the tested joint faces camera, then click Start Assessment.
                </div>
              )}
            </div>
          </div>

          <aside>
            <div className="bg-blue-50 rounded p-3 mb-4">
              <h4 className="font-semibold mb-2">Select Joint</h4>
              <div className="flex flex-col gap-2">
                {Object.keys(EX_THRESH).map(k=>(
                  <button key={k} onClick={()=>setJoint(k)} className={`px-3 py-2 rounded text-left ${joint===k?'bg-blue-600 text-white':'bg-white'}`}>
                    <div className="font-medium">{EX_THRESH[k].label}</div>
                    <div className="text-xs text-gray-600">Min: {EX_THRESH[k].min}°, Expected: {EX_THRESH[k].expected}°</div>
                  </button>
                ))}
              </div>
            </div>

            <div className="bg-green-50 rounded p-3 mb-4">
              <h4 className="font-semibold mb-2">Assessment Controls</h4>
              <div className="flex flex-col gap-2">
                <button onClick={startAssessment} disabled={running} className="px-3 py-2 bg-blue-600 text-white rounded disabled:opacity-60">Start Assessment (15s)</button>
                <button onClick={() => { setRunning(false); try{ if(cameraRef.current) cameraRef.current.stop(); }catch(e){} setCameraOn(false); setStatusMsg('Stopped'); }} className="px-3 py-2 bg-gray-200 rounded">Stop</button>
                <button onClick={()=>{ samplesRef.current={left:[],right:[]}; setReport(null); setSecondsLeft(15); setStatusMsg('Reset'); }} className="px-3 py-2 bg-white border rounded">Clear Data</button>
              </div>
            </div>

            <div className="bg-yellow-50 rounded p-3">
              <h4 className="font-semibold mb-2">Tips</h4>
              <ul className="text-sm list-disc pl-5">
                <li>Use frontal view so shoulders/hips are visible.</li>
                <li>Good lighting improves detection.</li>
                <li>Stand ~1.5–2m from camera for full body view.</li>
              </ul>
            </div>
          </aside>
        </div>
      </div>
    </div>
  );
}

/* render and create icons */
ReactDOM.createRoot(document.getElementById('root')).render(<FinalROMApp />);
if (window.lucide && window.lucide.createIcons) window.lucide.createIcons();
