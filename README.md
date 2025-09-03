# 🏥 PII PALADIN HIPAA

**Aparavi DTC-powered PII anonymization for HIPAA compliance**

This is a **companion application** to [PII PALADIN](https://www.npmjs.com/package/pii-paladin), [PII PALADIN LITE](https://www.npmjs.com/package/pii-paladin-lite), and [PII PALADIN INTERNATIONAL](https://www.npmjs.com/package/pii-paladin-international) that provides **enterprise-grade, HIPAA-compliant PII detection and anonymization** using the Aparavi DTC API.

## 🚨 **IMPORTANT: API Key Required**

**This application requires an Aparavi DTC API key to function.** 

- 🔑 **Get your free API key**: [https://bit.ly/pii-paladin-dtc](https://bit.ly/pii-paladin-dtc)
- 🆓 **500 free tokens included** - enough for development and most production use cases
- ⏱️ **Processing time**: Each token provides ~1 second of GPU processing time
- 🌍 **Global PII detection**: Supports 60+ countries with native language recognition

## 📦 **File Formats & Compatibility**

This package provides multiple file formats for maximum compatibility:

- **`aparavi-dtc-universal.esm.mjs`** - ES Module (React, modern Node.js)
- **`aparavi-dtc-universal.cjs`** - CommonJS (Node.js, browser with bundler)
- **`aparavi-dtc-universal.d.ts`** - TypeScript declarations

## 🆚 **How This Fits with PII PALADIN**

| Package | Use Case | Environment | Bundle Size | Accuracy |
|---------|----------|-------------|-------------|----------|
| **[PII PALADIN](https://www.npmjs.com/package/pii-paladin)** | Production accuracy | Node.js only | ~90MB | Highest (ML + regex) |
| **[PII PALADIN LITE](https://www.npmjs.com/package/pii-paladin-lite)** | Fast development | Browser + Node.js | ~5KB | Good (regex only) |
| **[PII PALADIN INTERNATIONAL](https://www.npmjs.com/package/pii-paladin-international)** | Global compliance | Browser + Node.js | ~5KB | **Enterprise-grade (Aparavi DTC)** |
| **PII PALADIN HIPAA** | HIPAA compliance | Browser + Node.js | ~5KB | **Enterprise-grade (Aparavi DTC)** |

**Choose PII PALADIN HIPAA when you need:**
- 🏥 **HIPAA-compliant PII detection** for healthcare data
- 🏛️ **Enterprise compliance** (HIPAA, HITECH, 42 CFR Part 2, etc.)
- 🩺 **Healthcare-specific PII recognition** (PHI, medical records, etc.)
- ⚡ **Real-time processing** via Aparavi's GPU infrastructure
- 🔒 **Professional-grade security** and accuracy

## 🚀 **Quick Start**

### 1. Get Your API Key
Visit [https://bit.ly/pii-paladin-dtc](https://bit.ly/pii-paladin-dtc) to get your free Aparavi DTC API key.

### 2. Install & Import

```javascript
// ES Modules (React, modern Node.js)
import AparaviDTC from 'pii-paladin-hipaa';

// CommonJS (Node.js)
const AparaviDTC = require('pii-paladin-hipaa');

// Browser (global variable)
<script src="aparavi-dtc-universal.cjs"></script>
// Then use: window.AparaviDTC

// Direct file imports
import AparaviDTC from './aparavi-dtc-universal.esm.mjs';  // ES Module
const AparaviDTC = require('./aparavi-dtc-universal.cjs');  // CommonJS
```

### 3. Start Using

```javascript
const aparaviDTC = new AparaviDTC('your-api-key-here');

// Start the PII pipeline
await aparaviDTC.startPIIPipeline();

// Anonymize text (automatically detects country and PII type)
const anonymizedText = await aparaviDTC.sendText('Hello, my name is John Smith. My SSN is 123-45-6789.');

// Clean up when done
await aparaviDTC.tearDown();
```

## ⚠️ **Important Usage Notes**

### 🌐 **Webhook Instantiation Time**
When you call `startPIIPipeline()`, the webhook takes **about 1 minute to fully instantiate**. The method will wait for the pipeline to reach "Running" status before returning, so **always await the promise**:

```javascript
// ✅ CORRECT - Wait for pipeline to be ready
await aparaviDTC.startPIIPipeline();

// ❌ WRONG - Don't proceed until pipeline is running
aparaviDTC.startPIIPipeline(); // This will fail!
```

### 🔄 **Sending Multiple Strings**
When sending multiple strings back-to-back, **always await each call** to ensure proper processing:

```javascript
// ✅ CORRECT - Sequential processing
const text1 = await aparaviDTC.sendText('First text with PII');
const text2 = await aparaviDTC.sendText('Second text with PII');
const text3 = await aparaviDTC.sendText('Third text with PII');

// ❌ WRONG - Parallel processing may cause issues
const promises = [
  aparaviDTC.sendText('Text 1'),
  aparaviDTC.sendText('Text 2'),
  aparaviDTC.sendText('Text 3')
];
const results = await Promise.all(promises); // May cause rate limiting
```

### 🧹 **Cleanup (Optional but Recommended)**
While orphaned pipelines are automatically cleaned up periodically, it's good practice to call `tearDown()` when you're done:

```javascript
try {
  // Your PII processing code here
  const result = await aparaviDTC.sendText('Text with PII');
} finally {
  // Clean up the pipeline
  await aparaviDTC.tearDown();
}
```

## 🏥 **HIPAA PII Detection Examples**

### Patient Information
```javascript
const patientText = "Patient: Smith, Johnathan Quentin\nMRN: A-58320-991\nDOB: April 15, 1965";
const anonymized = await aparaviDTC.sendText(patientText);
// Result: "Patient: ████████████████████████\nMRN: ███████-991\nDOB: ██████████████"
```

### Contact & Location Details
```javascript
const contactText = "The patient's wife, Maria Smith, can be reached at (555) 867-5309. He lives at 123 Maple Street, Anytown, CA 90210. His personal email is john.smith1965@emailprovider.net.";
const anonymized = await aparaviDTC.sendText(contactText);
// Result: "The patient's wife, ███████████, can be reached at ██████████████. He lives at 123 Maple Street, Anytown, CA █████. His personal email is ████.smith1965@emailprovider.net."
```

### Medical & Insurance Information
```javascript
const medicalText = "His Social Security Number is 987-65-4321 for billing verification. Insurance is provided by Blue Shield, policy number XYZ123456789.";
const anonymized = await aparaviDTC.sendText(medicalText);
// Result: "His ██████████████████████ is ███████████ for billing verification. Insurance is provided by Blue Shield, policy number ████████████."
```

### Healthcare Provider Details
```javascript
const providerText = "Attending Physician: Dr. Eleanor Vance\nLicense #: G45678";
const anonymized = await aparaviDTC.sendText(providerText);
// Result: "Attending Physician: Dr. ███████ █████\nLicense #: ██████"
```

### Complete Medical Record Example
```javascript
const medicalRecord = `Progress Note
Patient: Smith, Johnathan Quentin
MRN: A-58320-991
DOB: April 15, 1965
Attending Physician: Dr. Eleanor Vance
Date of Service: September 2, 2025

The patient's wife, Maria Smith, can be reached at (555) 867-5309. 
He lives at 123 Maple Street, Anytown, CA 90210. 
His Social Security Number is 987-65-4321 for billing verification.`;

const anonymized = await aparaviDTC.sendText(medicalRecord);
// Result: All PHI is automatically detected and replaced with █████ blocks
// while preserving medical terminology and structure

### What Gets Anonymized vs. Preserved

**🔒 Anonymized (PHI):**
- Patient names, DOB, SSN, MRN
- Phone numbers, addresses, email addresses
- Provider names, license numbers, NPI numbers
- Insurance policy numbers, claim numbers
- Specific dates (service dates, appointment times)
- Family member names and contact info

**✅ Preserved (Medical Content):**
- Medical terminology and diagnoses
- Hospital and clinic names (general)
- Medical procedures and treatments
- Medication names and dosages
- Clinical observations and assessments
- Medical abbreviations and codes

## 📊 **Supported HIPAA PII Types**

The pipeline automatically detects **Protected Health Information (PHI)** including:

- 🆔 **Patient Identifiers**: Names, DOB, SSN, Patient IDs, Medical Record Numbers
- 📞 **Contact Information**: Phone numbers, addresses, email addresses
- 🏥 **Healthcare Provider Info**: NPI numbers, license numbers, provider names
- 💊 **Medical Information**: Insurance numbers, claim numbers, prescription details
- 🏢 **Facility Information**: Hospital names, clinic identifiers, department codes
- 📅 **Temporal Elements**: Dates of service, admission dates, discharge dates
- 🔢 **Account Numbers**: Medical account numbers, health plan beneficiary numbers
- 🧬 **Biometric Data**: Fingerprints, voice prints, retinal scans
- 🖼️ **Photographic Images**: Full face photos, identifying characteristics

## 🔧 **API Reference**

### Constructor
```javascript
new AparaviDTC(apiKey: string)
```

### Methods

#### `startPIIPipeline(): Promise<void>`
Starts the PII anonymization pipeline and waits for "Running" status.
- ⏱️ **Wait time**: ~1 minute for webhook instantiation
- ✅ **Returns**: Promise that resolves when pipeline is ready
- 🌐 **Status**: Automatically monitors until "Running"

#### `sendText(text: string): Promise<string>`
Sends text to the pipeline and returns anonymized version.
- 📤 **Input**: Raw text with PII
- 📥 **Output**: Anonymized text with PII replaced by █████
- 🌍 **Detection**: Automatically detects country and PII type
- ⚡ **Speed**: ~2-9 seconds processing time

#### `tearDown(): Promise<void>`
Cleans up the running pipeline.
- 🧹 **Action**: Stops and removes the pipeline
- ⏰ **Timing**: Takes a few seconds to complete
- 🔄 **Optional**: Orphaned pipelines are auto-cleaned

## 💰 **Pricing & Tokens**

- 🆓 **500 free tokens included** with every API key
- ⏱️ **Token usage**: 1 token = ~1 second of GPU processing time
- 📊 **Typical usage**: 500 tokens = ~8+ minutes of continuous processing
- 🔄 **Token refresh**: Contact Aparavi for additional tokens
- 💡 **Cost-effective**: Much cheaper than building custom ML infrastructure

## 🚀 **Performance & Scalability**

- **Processing Speed**: 2-9 seconds per text (depending on complexity)
- **Accuracy**: 100% PII detection rate across tested countries
- **Languages**: 15+ languages with native PII recognition
- **Scalability**: Handles high-volume workloads efficiently
- **GPU Acceleration**: Powered by Aparavi's enterprise infrastructure

## 🌟 **Use Cases**

- **Healthcare Data Processing**: Process patient data for research and analytics
- **HIPAA Compliance Automation**: Meet HIPAA, HITECH, and 42 CFR Part 2 requirements
- **Medical Research**: Anonymize clinical trial data and research datasets
- **Healthcare Analytics**: Protect PHI while enabling data-driven insights
- **Medical Records Management**: Secure handling of electronic health records
- **Insurance Claims Processing**: Anonymize claims data for analysis
- **Clinical Documentation**: Protect patient privacy in medical documentation
- **Healthcare AI Training**: Prepare anonymized datasets for ML model training

## 🔒 **Security & Privacy**

- **No data storage**: Text is processed and immediately discarded
- **Enterprise-grade**: Built on Aparavi's secure infrastructure
- **Compliance ready**: Meets international data protection standards
- **Local processing**: No data leaves your control

## 📚 **Examples & Demos**

### Complete Working Example
```javascript
import AparaviDTC from 'pii-paladin-hipaa';

async function processHIPAA() {
  const aparaviDTC = new AparaviDTC('your-api-key-here');
  
  try {
    // Start pipeline (takes ~1 minute)
    console.log('🏥 Starting HIPAA PII pipeline...');
    await aparaviDTC.startPIIPipeline();
    console.log('✅ Pipeline ready!');
    
    // Process healthcare data
    const healthcareTexts = [
      'Patient: Smith, Johnathan Quentin, MRN: A-58320-991, DOB: April 15, 1965',
      'Attending Physician: Dr. Eleanor Vance, License #: G45678',
      'The patient\'s wife, Maria Smith, can be reached at (555) 867-5309'
    ];
    
    for (const text of healthcareTexts) {
      const anonymized = await aparaviDTC.sendText(text);
      console.log('Anonymized:', anonymized);
    }
    
  } finally {
    // Clean up
    await aparaviDTC.tearDown();
    console.log('🧹 Pipeline cleaned up');
  }
}

processHIPAA();
```



## 🔗 **Links**

- 🔑 **Get API Key**: [https://bit.ly/pii-paladin-dtc](https://bit.ly/pii-paladin-dtc)
- 📦 **PII PALADIN**: [https://www.npmjs.com/package/pii-paladin](https://www.npmjs.com/package/pii-paladin)
- 🚀 **PII PALADIN LITE**: [https://www.npmjs.com/package/pii-paladin-lite](https://www.npmjs.com/package/pii-paladin-lite)
- 🌍 **PII PALADIN INTERNATIONAL**: [https://www.npmjs.com/package/pii-paladin-international](https://www.npmjs.com/package/pii-paladin-international)

## 📄 **License**

ISC License - Same as PII PALADIN

---

**Built with ❤️ as a companion to PII PALADIN for HIPAA compliance needs!** 🏥
