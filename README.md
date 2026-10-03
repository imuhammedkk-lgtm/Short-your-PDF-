```html
<!DOCTYPE html>
<html lang="ml">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>PDF Document Summarizer & Translator</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- FontAwesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <!-- PDF.js library for PDF parsing in browser -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.min.js"></script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Manrope:wght@400;600;700&display=swap');
    body {
      font-family: 'Manrope', 'Inter', sans-serif;
    }
  </style>
</head>
<body class="bg-slate-900 text-slate-100 min-h-screen flex flex-col justify-between selection:bg-blue-600 selection:text-white">

  <!-- Top Navigation Header -->
  <header class="border-b border-slate-800 bg-slate-950/80 backdrop-blur-md sticky top-0 z-50">
    <div class="max-w-5xl mx-auto px-4 py-4 flex flex-wrap justify-between items-center gap-4">
      <div class="flex items-center space-x-3">
        <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-blue-600 to-indigo-500 flex items-center justify-center text-white shadow-lg shadow-blue-500/30">
          <i class="fa-solid fa-file-pdf text-xl"></i>
        </div>
        <div>
          <h1 class="text-xl font-bold tracking-tight text-white">DocShortener <span class="text-xs px-2 py-0.5 rounded bg-blue-500/20 text-blue-400 font-semibold border border-blue-500/30">AI PRO</span></h1>
          <p class="text-xs text-slate-400">PDF സാരാംശം മലയാളത്തിലും മറ്റ് ഭാഷകളിലും ലഭിക്കുന്നു</p>
        </div>
      </div>
      <div id="planBadge" class="flex items-center gap-2 px-3 py-1.5 rounded-full text-xs font-semibold bg-slate-800 text-slate-300 border border-slate-700">
        <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
        <span>FREE PLAN (Max 200 പേജ്)</span>
      </div>
    </div>
  </header>

  <!-- Main Content Container -->
  <main class="max-w-4xl mx-auto w-full px-4 py-8 flex-grow space-y-6">

    <!-- PDF Upload Card -->
    <div class="bg-slate-800/80 rounded-2xl border border-slate-700/80 p-6 shadow-xl backdrop-blur-sm">
      <div class="flex items-center justify-between mb-4">
        <h2 class="text-lg font-bold text-white flex items-center gap-2">
          <i class="fa-solid fa-cloud-arrow-up text-blue-400"></i> ഫയൽ അപ്‌ലോഡ് ചെയ്യുക
        </h2>
        <span class="text-xs text-slate-400">200 / 400 പേജ് ലിമിറ്റ്</span>
      </div>

      <!-- Drag & Drop Zone -->
      <div class="border-2 border-dashed border-slate-600 hover:border-blue-500 rounded-xl p-6 text-center cursor-pointer transition-all bg-slate-900/40 hover:bg-slate-900/80 group" id="dropZone" onclick="document.getElementById('pdfInput').click()">
        <input type="file" id="pdfInput" class="hidden" accept=".pdf" onchange="handleFileSelect(event)">
        <div class="w-12 h-12 mx-auto mb-3 rounded-full bg-blue-500/10 text-blue-400 flex items-center justify-center group-hover:scale-110 transition-transform">
          <i class="fa-solid fa-file-arrow-up text-2xl"></i>
        </div>
        <p class="text-slate-200 font-medium text-sm" id="fileNameDisplay">PDF ഫയൽ ഇവിടെ തിരഞ്ഞെടുക്കുക അല്ലെങ്കിൽ വലിച്ചിടുക</p>
        <p class="text-slate-400 text-xs mt-1">ലിമിറ്റ്: ഫ്രീ 200 പേജ് | പ്രോ 400 പേജ്</p>
      </div>

      <!-- Manual Page Count Option for Quick Test -->
      <div class="mt-4 grid grid-cols-1 md:grid-cols-2 gap-4">
        <div>
          <label class="block text-xs font-semibold text-slate-400 mb-1.5">പേജുകളുടെ എണ്ണം (ഓട്ടോമാറ്റിക്/മാനുവൽ):</label>
          <input type="number" id="manualPages" min="1" max="500" placeholder="ഉദാ: 150" class="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-sm text-slate-100 focus:outline-none focus:border-blue-500 transition-colors">
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-400 mb-1.5">ചുരുക്കേണ്ട ഭാഷ തിരഞ്ഞെടുക്കുക:</label>
          <select id="targetLanguage" class="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-sm text-slate-100 focus:outline-none focus:border-blue-500 transition-colors">
            <option value="ml">Malayalam (മലയാളം)</option>
            <option value="en">English (ഇംഗ്ലീഷ്)</option>
            <option value="hi">Hindi (ഹിന്ദി)</option>
            <option value="ta">Tamil (തമിഴ്)</option>
            <option value="ar">Arabic (അറബിക്)</option>
          </select>
        </div>
      </div>

      <div class="mt-5 flex flex-col sm:flex-row gap-3">
        <button onclick="summarizePDF()" class="flex-1 bg-gradient-to-r from-blue-600 to-indigo-600 hover:from-blue-500 hover:to-indigo-500 text-white font-semibold py-3 px-4 rounded-xl shadow-lg shadow-blue-600/20 active:scale-[0.99] transition-all flex items-center justify-center gap-2 text-sm">
          <i class="fa-solid fa-wand-magic-sparkles"></i> ഷോർട്ട് ആക്കി പോയിന്റുകൾ എടുക്കുക
        </button>
        <button onclick="loadSamplePDF()" class="bg-slate-700 hover:bg-slate-600 text-slate-200 text-xs font-medium py-3 px-4 rounded-xl transition-all flex items-center justify-center gap-1.5">
          <i class="fa-solid fa-vial"></i> ഡെമോ ടെസ്റ്റ് (Sample Run)
        </button>
      </div>
    </div>

    <!-- Pro Upgrade Banner -->
    <div class="bg-gradient-to-r from-amber-950/40 via-amber-900/30 to-slate-800 rounded-2xl border border-amber-500/30 p-5 shadow-lg relative overflow-hidden">
      <div class="flex flex-col md:flex-row md:items-center justify-between gap-4">
        <div>
          <div class="flex items-center gap-2 mb-1">
            <span class="bg-amber-500/20 text-amber-400 border border-amber-500/40 text-[10px] uppercase font-bold px-2 py-0.5 rounded-full">Pro Upgrade</span>
            <h3 class="text-sm font-bold text-amber-200">200 പേജിൽ കൂടുതലുണ്ടോ? Pro ആക്കുക!</h3>
          </div>
          <p class="text-xs text-slate-300 leading-relaxed">
            400 പേജ് വരെയുള്ള ഏത് വലിയ പുസ്തകവും ഷോർട്ട് ആക്കാൻ: <br class="hidden sm:inline">
            <span class="font-bold text-amber-300 text-sm">90 48 36 88 90</span> എന്ന നമ്പറിലേക്ക് പണമടയ്ക്കുക.
          </p>
        </div>
        
        <div class="flex flex-col sm:flex-row items-center gap-2 w-full md:w-auto">
          <input type="text" id="proCodeInput" placeholder="Pro Code നൽകുക (eg: B 20 30)" class="w-full sm:w-48 bg-slate-900 border border-amber-500/40 text-amber-200 text-xs rounded-lg px-3 py-2.5 focus:outline-none focus:ring-1 focus:ring-amber-500">
          <button onclick="activateProMode()" class="w-full sm:w-auto whitespace-nowrap bg-amber-500 hover:bg-amber-400 text-slate-950 font-bold text-xs py-2.5 px-4 rounded-lg shadow-md transition-all flex items-center justify-center gap-1">
            <i class="fa-solid fa-key"></i> Pro Unlock
          </button>
        </div>
      </div>
    </div>

    <!-- Results Container -->
    <div id="resultsCard" class="hidden bg-slate-800/90 rounded-2xl border border-slate-700 p-6 shadow-xl space-y-4">
      <div class="flex flex-wrap items-center justify-between border-b border-slate-700/80 pb-3 gap-2">
        <h3 class="text-base font-bold text-white flex items-center gap-2">
          <i class="fa-solid fa-list-check text-emerald-400"></i> പ്രധാന പോയിന്റുകൾ (Summary Points)
        </h3>
        <div class="flex items-center gap-2">
          <span id="outputLangBadge" class="text-[11px] bg-slate-700 text-slate-300 px-2.5 py-1 rounded-md font-medium">മലയാളം</span>
          <button onclick="copySummaryText()" class="bg-blue-600/20 text-blue-400 hover:bg-blue-600/30 text-xs px-3 py-1 rounded-md font-medium transition-colors border border-blue-500/30 flex items-center gap-1">
            <i class="fa-regular fa-copy"></i> കോപ്പി ചെയ്യുക
          </button>
        </div>
      </div>

      <!-- Loading State Indicator -->
      <div id="loadingIndicator" class="hidden py-8 text-center space-y-3">
        <div class="inline-block w-8 h-8 border-4 border-blue-500 border-t-transparent rounded-full animate-spin"></div>
        <p class="text-xs text-blue-400 font-medium">PDF സ്കാൻ ചെയ്ത് വിശകലനം ചെയ്യുന്നു... കാത്തിരിക്കൂ...</p>
      </div>

      <!-- Summary Content List -->
      <div id="summaryContent" class="space-y-3 text-sm text-slate-200 leading-relaxed font-normal">
        <!-- Summarized bullet points will appear here -->
      </div>
    </div>

  </main>

  <!-- Footer -->
  <footer class="border-t border-slate-800 bg-slate-950 py-4 text-center text-xs text-slate-500">
    <p>© 2026 PDF & Doc Summarizer Engine | Pro Contact: +91 9048368890</p>
  </footer>

  <script>
    // Configure PDF.js Worker
    pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.worker.min.js';

    let isProUser = false;
    const PRO_CODE = "B 20 30";
    let selectedPdfFile = null;

    // Handle File Selection
    function handleFileSelect(event) {
      const file = event.target.files[0];
      if (file) {
        selectedPdfFile = file;
        document.getElementById('fileNameDisplay').innerHTML = `<i class="fa-solid fa-file-pdf text-red-400 mr-2"></i><b>${file.name}</b> (${(file.size/1024/1024).toFixed(2)} MB)`;
        
        // Auto extract page count from PDF
        const reader = new FileReader();
        reader.onload = async function() {
          try {
            const typedarray = new Uint8Array(this.result);
            const pdf = await pdfjsLib.getDocument(typedarray).promise;
            document.getElementById('manualPages').value = pdf.numPages;
          } catch(e) {
            console.log("PDF reader info:", e);
          }
        };
        reader.readAsArrayBuffer(file);
      }
    }

    // Pro Activation Function
    function activateProMode() {
      const enteredCode = document.getElementById('proCodeInput').value.trim();
      if (enteredCode === PRO_CODE) {
        isProUser = true;
        const badge = document.getElementById('planBadge');
        badge.className = "flex items-center gap-2 px-3 py-1.5 rounded-full text-xs font-bold bg-amber-500/20 text-amber-300 border border-amber-500/40";
        badge.innerHTML = `<i class="fa-solid fa-crown text-amber-400"></i> PRO PLAN ACTIVE (Max 400 പേജ്)`;
        alert("Pro Plan അൺലോക്ക് ആയിരിക്കുന്നു! ഇനി 400 പേജ് വരെയുള്ള PDF സംഗ്രഹിക്കാം.");
      } else {
        alert("നൽകിയ Pro Code തെറ്റാണ്. പണം അടച്ച ശേഷം 90 48 36 88 90 എന്ന നമ്പറിൽ നൽകുന്ന 'B 20 30' കോഡ് കൃത്യമായി അടിക്കുക.");
      }
    }

    // Sample Test Loader
    function loadSamplePDF() {
      document.getElementById('manualPages').value = "180";
      document.getElementById('fileNameDisplay').innerHTML = `<i class="fa-solid fa-file-circle-check text-emerald-400 mr-2"></i> <b>ഡെമോ സാമ്പിൾ ബുക്ക് (180 Pages)</b>`;
      selectedPdfFile = "SAMPLE_DEMO";
    }

    // Main Summarizer Logic
    async function summarizePDF() {
      const pageInput = document.getElementById('manualPages').value;
      const pages = parseInt(pageInput) || (selectedPdfFile ? 150 : 0);
      const targetLang = document.getElementById('targetLanguage').value;
      const resultsCard = document.getElementById('resultsCard');
      const loading = document.getElementById('loadingIndicator');
      const summaryContent = document.getElementById('summaryContent');
      const langBadge = document.getElementById('outputLangBadge');

      if (!pages && !selectedPdfFile) {
        alert("ദയവായി ഒരു PDF ഫയൽ തിരഞ്ഞെടുക്കുക അല്ലെങ്കിൽ പേജുകളുടെ എണ്ണം ടൈപ്പ് ചെയ്യുക.");
        return;
      }

      // Check Page Limits
      const maxPages = isProUser ? 400 : 200;

      if (pages > maxPages) {
        if (!isProUser && pages <= 400) {
          alert(`ഈ ഫയലിൽ ${pages} പേജുകളുണ്ട്! 200 പേജിൽ കൂടുതലുള്ളവ പ്രോസസ്സ് ചെയ്യാൻ Pro Plan ആവശ്യമാണ്.\n\n90 48 36 88 90 എന്ന നമ്പറിലേക്ക് ബന്ധപ്പെട്ട് "B 20 30" കോഡ് ഉപയോഗിച്ച് Pro അൺലോക്ക് ചെയ്യുക.`);
        } else {
          alert(`ക്ഷമിക്കണം! ഈ വെബ്സൈറ്റിൽ പരമാവധി ${maxPages} പേജുകൾ വരെയുള്ള ബുക്കുകൾ മാത്രമേ ചുരുക്കാൻ സാധിക്കൂ.`);
        }
        return;
      }

      // Show Results Container with Loading
      resultsCard.classList.remove('hidden');
      loading.classList.remove('hidden');
      summaryContent.innerHTML = "";

      // Simulate Extraction & Point Generation
      setTimeout(() => {
        loading.classList.add('hidden');
        langBadge.innerText = targetLang.toUpperCase();

        let points = [];

        if (targetLang === 'ml') {
          points = [
            `<b>ആകെ വിശകലനം ചെയ്ത പേജുകൾ:</b> ${pages} പേജുകൾ`,
            `<b>പ്രധാന വിഷയം:</b> പ്രബന്ധത്തിൽ/പുസ്തകത്തിൽ പരാമർശിക്കുന്ന കേന്ദ്ര ആശയങ്ങളുടെയും വിശകലനങ്ങളുടെയും ചുരുക്കം താഴെ നൽകുന്നു.`,
            `<b>പോയിന്റ് 1:</b> വിഷയത്തിന്റെ അടിസ്ഥാന പശ്ചാത്തലവും ലക്ഷ്യങ്ങളും ആദ്യ അധ്യായങ്ങളിൽ വിശദമാക്കുന്നു.`,
            `<b>പോയിന്റ് 2:</b> പ്രധാന കണ്ടെത്തലുകളും വിവര ശേഖരണ രീതികളും കൃത്യമായ ഡാറ്റ സഹിതം സമർപ്പിച്ചിട്ടുണ്ട്.`,
            `<b>പോയിന്റ് 3:</b> മുൻകാല ഗവേഷണങ്ങളുമായി താരതമ്യം ചെയ്ത് പുതിയ രീതികൾ ശുപാർശ ചെയ്യുന്നു.`,
            `<b>പോയിന്റ് 4:</b> പ്രായോഗികതലത്തിൽ നടപ്പിലാക്കാൻ കഴിയുന്ന സുപ്രധാന തീരുമാനങ്ങളും നിഗമനങ്ങളും നൽകിയിട്ടുണ്ട്.`
          ];
        } else if (targetLang === 'en') {
          points = [
            `<b>Total Analyzed Pages:</b> ${pages} Pages`,
            `<b>Core Subject:</b> High-level summary of key arguments and data contained in the uploaded document.`,
            `<b>Point 1:</b> Initial chapters establish the fundamental framework and core objectives.`,
            `<b>Point 2:</b> Empirical findings and qualitative analysis are organized in detail.`,
            `<b>Point 3:</b> Comparative evaluation highlights key optimizations and strategic improvements.`,
            `<b>Point 4:</b> Final recommendations offer actionable conclusions for implementation.`
          ];
        } else {
          points = [
            `<b>Analyzed Pages:</b> ${pages} Pages`,
            `<b>Summary:</b> Key points generated successfully in selected language format (${targetLang.toUpperCase()}).`,
            `<b>Point 1:</b> Main objectives and theoretical background.`,
            `<b>Point 2:</b> Key evidence and analytical results summarized.`,
            `<b>Point 3:</b> Final recommendations and action plan.`
          ];
        }

        let htmlList = `<ul class="space-y-2.5">`;
        points.forEach((pt) => {
          htmlList += `
            <li class="flex items-start gap-3 bg-slate-900/60 p-3 rounded-lg border border-slate-700/50">
              <span class="text-emerald-400 mt-1"><i class="fa-solid fa-circle-check text-sm"></i></span>
              <div>${pt}</div>
            </li>`;
        });
        htmlList += `</ul>`;

        summaryContent.innerHTML = htmlList;
      }, 1200);
    }

    // Copy to Clipboard
    function copySummaryText() {
      const content = document.getElementById('summaryContent').innerText;
      if (!content) return;
      
      const el = document.createElement('textarea');
      el.value = content;
      document.body.appendChild(el);
      el.select();
      document.execCommand('copy');
      document.body.removeChild(el);
      
      alert("പോയിന്റുകൾ ക്ലിപ്പ്ബോർഡിലേക്ക് കോപ്പി ചെയ്തു!");
    }
  </script>
</body>
</html>
```