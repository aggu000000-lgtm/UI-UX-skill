# Ethical & Privacy-Centered UX Reference

> **2026 STANDARDS**: Privacy is no longer a feature — it's a requirement. Users actively evaluate privacy as part of their UX experience. This guide covers patterns for building trust through transparent, ethical design.

---

## PRIVACY-BY-DESIGN PRINCIPLES

### Core Framework

1. **Data Minimization**: Collect only what's necessary
2. **Purpose Limitation**: Use data only for stated purposes
3. **Transparency**: Be clear about what you collect
4. **User Control**: Give users power over their data
5. **Security**: Protect what you do collect
6. **Accountability**: Be responsible for data handling

---

## CONSENT PATTERNS

### Granular Cookie Consent

```css
.consent-banner {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 1.5rem;
  background: var(--surface-primary);
  border-top: 1px solid var(--border-subtle);
  z-index: 1000;
}

.consent-options {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  margin: 1rem 0;
}

.consent-toggle {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.75rem 1rem;
  background: var(--bg-tertiary);
  border-radius: 8px;
  cursor: pointer;
}

.consent-toggle input:checked + span {
  color: var(--accent-primary);
}
```

### Consent Component

```jsx
function CookieConsent() {
  const [preferences, setPreferences] = useState({
    necessary: true,  // Always on
    analytics: false,
    marketing: false,
    personalization: false
  });
  
  const handleSave = () => {
    savePreferences(preferences);
    setShowBanner(false);
  };
  
  return (
    <div className="consent-banner">
      <h2>We value your privacy</h2>
      <p>
        We use cookies to enhance your experience. 
        Choose what you're comfortable with:
      </p>
      
      <div className="consent-options">
        <ToggleOption 
          label="Necessary"
          description="Required for the site to work"
          checked={preferences.necessary}
          disabled
        />
        <ToggleOption 
          label="Analytics"
          description="Help us improve our site"
          checked={preferences.analytics}
          onChange={(v) => setPreferences({...preferences, analytics: v})}
        />
        <ToggleOption 
          label="Marketing"
          description="Personalized advertisements"
          checked={preferences.marketing}
          onChange={(v) => setPreferences({...preferences, marketing: v})}
        />
        <ToggleOption 
          label="Personalization"
          description="Remember your preferences"
          checked={preferences.personalization}
          onChange={(v) => setPreferences({...preferences, personalization: v})}
        />
      </div>
      
      <div className="consent-actions">
        <button onClick={handleSave}>Accept All</button>
        <button onClick={handleSave}>Save Preferences</button>
        <button className="text-btn">Privacy Policy</button>
      </div>
    </div>
  );
}
```

---

## DATA TRANSPARENCY

### Data Usage Dashboard

```jsx
function PrivacyDashboard() {
  const [dataCategories, setDataCategories] = useState([
    {
      category: 'Browsing History',
      items: 1_234,
      lastUpdated: '2024-01-15',
      deletable: true
    },
    {
      category: 'Location Data',
      items: 89,
      lastUpdated: '2024-01-14',
      deletable: true
    },
    {
      category: 'Purchase History',
      items: 47,
      lastUpdated: '2024-01-10',
      deletable: false
    }
  ]);
  
  return (
    <div className="privacy-dashboard">
      <h2>Your Data</h2>
      
      <div className="data-category-list">
        {dataCategories.map(cat => (
          <div key={cat.category} className="data-category">
            <div className="category-info">
              <h3>{cat.category}</h3>
              <p>
                {cat.items} items • Updated {cat.lastUpdated}
              </p>
            </div>
            {cat.deletable && (
              <button className="delete-btn">Delete</button>
            )}
          </div>
        ))}
      </div>
      
      <button className="export-all-btn">
        <DownloadIcon />
        Export All Data
      </button>
      
      <button className="delete-all-btn danger">
        Delete All Data
      </button>
    </div>
  );
}
```

---

## TRACKING TRANSPARENCY

### Tracking Indicator

```css
.tracking-indicator {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 4px 10px;
  background: var(--bg-tertiary);
  border-radius: 20px;
  font-size: 12px;
  color: var(--text-muted);
}

.tracking-indicator::before {
  content: '';
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: var(--status-neutral);
}

.tracking-indicator.active::before {
  background: var(--status-warning);
  animation: pulse 2s infinite;
}
```

### Privacy Nutrition Labels

```jsx
function PrivacyLabel({ data }) {
  return (
    <div className="privacy-label">
      <div className="label-header">
        <ShieldIcon />
        <span>Privacy Info</span>
      </div>
      
      <ul className="label-items">
        <li className={data.location ? 'active' : 'inactive'}>
          Location: {data.location ? 'Collected' : 'Not collected'}
        </li>
        <li className={data.contacts ? 'active' : 'inactive'}>
          Contacts: {data.contacts ? 'Shared' : 'Not shared'}
        </li>
        <li className={data.analytics ? 'active' : 'inactive'}>
          Analytics: {data.analytics ? 'Enabled' : 'Disabled'}
        </li>
      </ul>
    </div>
  );
}
```

---

## ANONYMIZATION PATTERNS

### Local Processing Indicator

```css
.processing-local {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  background: var(--success-bg);
  border: 1px solid var(--success-border);
  border-radius: 6px;
  color: var(--success-text);
}

.processing-local svg {
  color: var(--success-icon);
}
```

### Edge Computing Badge

```jsx
function DataProcessingInfo({ mode }) {
  if (mode === 'local') {
    return (
      <span className="processing-local">
        <DeviceIcon />
        Processed on your device
      </span>
    );
  }
  
  return (
    <span className="processing-cloud">
      <CloudIcon />
      Processed on servers
    </span>
  );
}
```

---

## DARK PATTERN AVOIDANCE

### Checklist for Common Dark Patterns

```markdown
## Dark Patterns to Avoid

### Trick the User
- [ ] Pre-checked opt-in boxes
- [ ] Hidden unsubscribe buttons
- [ ] Misdirection in UI (confusing button labels)
- [ ] Bait and switch
- [ ] Confirm shaming ("No thanks, I don't want to save money")

### Hidden Costs
- [ ] Hidden fees revealed at checkout
- [ ] Subscription defaults without clear notice
- [ ] Auto-renewal without reminder

### Forced Action
- [ ] Requiring signup to purchase
- [ ] Mandatory data sharing to use service
- [ ] Preventing comparison shopping

###roach for Good UX
- [ ] Default to privacy-friendly options
- [ ] Make opt-out as easy as opt-in
- [ ] Be transparent about data practices
- [ ] Give users meaningful choices
```

---

## CONSENT-FIRST FORMS

### GDPR-Compliant Form

```jsx
function DataProcessingForm() {
  const [consents, setConsents] = useState({
    terms: false,
    marketing: false,
    thirdParty: false
  });
  
  const [errors, setErrors] = useState({});
  
  const validate = () => {
    if (!consents.terms) {
      setErrors({ terms: 'You must accept the terms' });
      return false;
    }
    return true;
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    if (validate()) {
      submitForm(consents);
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      {/* Form fields */}
      
      <div className="consent-section">
        <label className={errors.terms ? 'error' : ''}>
          <input
            type="checkbox"
            checked={consents.terms}
            onChange={(e) => setConsents({...consents, terms: e.target.checked})}
          />
          <span>
            I agree to the{' '}
            <a href="/terms">Terms of Service</a> and{' '}
            <a href="/privacy">Privacy Policy</a>
          </span>
        </label>
        {errors.terms && <span className="error">{errors.terms}</span>}
        
        <label>
          <input
            type="checkbox"
            checked={consents.marketing}
            onChange={(e) => setConsents({...consents, marketing: e.target.checked})}
          />
          <span>
            I want to receive marketing emails 
            <small>(Optional)</small>
          </span>
        </label>
        
        <label>
          <input
            type="checkbox"
            checked={consents.thirdParty}
            onChange={(e) => setConsents({...consents, thirdParty: e.target.checked})}
          />
          <span>
            I agree to share data with third-party partners
            <small>(Optional)</small>
          </span>
        </label>
      </div>
      
      <button type="submit">Create Account</button>
    </form>
  );
}
```

---

## LEGAL COMPLIANCE REFERENCES

| Regulation | Region | Key Requirements |
|------------|--------|------------------|
| GDPR | EU | Consent, data access, deletion rights |
| CCPA | California | Disclosure, opt-out rights |
| CPRA | California | Sensitive data protections |
| LGPD | Brazil | Consent, data portability |
| POPIA | South Africa | Lawful processing, security |
| PIPEDA | Canada | Consent, access, accountability |

---

## TOOLS & RESOURCES

- **GDPR Checklist**: https://gdpr.eu/checklist/
- **Privacy by Design**: https://www.ipc.on.ca/resource/privacy-by-design/
- **Dark Patterns Guide**: https://www.darkpatterns.org/
- **Privacy Nutrition Labels**: https://privacy.nnovation.org/

---

**Last Updated**: 2026-04-12
