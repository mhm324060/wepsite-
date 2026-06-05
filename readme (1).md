// تهيئة المستند بالعربية واتجاه RTL قبل رسم الهيكل المشترك.
const root = document.documentElement;
root.setAttribute("lang", "ar");
root.setAttribute("dir", "rtl");

const body = document.body;

const DEFAULT_LANGUAGE = "ar";
const SUPPORTED_LANGUAGES = new Set(["ar", "en"]);

const STORAGE_KEYS = {
  theme: "pet-paradise-theme",
  fontScale: "pet-paradise-font-scale",
  language: "pet-paradise-language",
};

const SESSION_KEYS = {
  // Requirement: one-time visibility logic using the key: loader-shown
  loaderShown: "loader-shown",
};

const CONTACT = {
  phoneDisplay: "0790956684",
  phoneHref: "0790956684",
  whatsappHref: "https://wa.me/962790956684",
  email: "sakkbus@gmail.com",
  location: {
    ar: "مرسى أيلة، العقبة، الأردن",
    en: "Ayla Marina, Aqaba, Jordan",
  },
};

// التنسيقات المحقونة وقت التشغيل تحافظ على ثبات الترويسة والطبقات العلوية بصريًا عبر الصفحات.
const RUNTIME_TUNING_STYLES = String.raw`
html {
  -webkit-text-size-adjust: 100%;
  text-size-adjust: 100%;
}

a,
button,
input,
select,
textarea {
  -webkit-tap-highlight-color: transparent;
}

a:focus:not(:focus-visible),
button:focus:not(:focus-visible),
input:focus:not(:focus-visible),
select:focus:not(:focus-visible),
textarea:focus:not(:focus-visible) {
  outline: none;
}

:root {
  --header-height: 5.4rem;
  --header-shell-gap: clamp(1rem, 1.8vw, 1.55rem);
  --header-brand-nav-gap: clamp(0.95rem, 1.8vw, 1.7rem);
  --scrollbar-compensation: 0px;
}

body {
  padding-inline-end: var(--scrollbar-compensation);
}

.topbar,
.live-footer,
.scroll-progress {
  padding-inline-end: var(--scrollbar-compensation);
}

.topbar {
  min-height: var(--header-height);
  transform: translateZ(0);
}

.topbar .mx-auto {
  min-height: calc(var(--header-height) - 1px);
  gap: var(--header-shell-gap);
  column-gap: var(--header-shell-gap);
  flex-wrap: nowrap;
}

.topbar .mx-auto > nav {
  margin-inline-start: var(--header-brand-nav-gap);
}

.header-brand {
  min-width: max-content;
  margin-inline-end: clamp(0.15rem, 0.75vw, 0.55rem);
}

.header-brand .brand-mark {
  width: clamp(4.2rem, 8vw, 4.95rem);
}

.header-brand-copy {
  align-content: center;
}

.header-actions {
  min-height: 3.2rem;
}

.topbar .utility-chip,
.topbar .btn-luxury,
.topbar .header-settings,
.topbar .header-settings-trigger,
.topbar .nav-link-icon,
.topbar .nav-link::after {
  will-change: auto;
}

.topbar .utility-chip:hover,
.topbar .utility-chip:focus-visible,
.topbar .btn-luxury:hover,
.topbar .btn-luxury:focus-visible,
.topbar .nav-link:hover .nav-link-icon,
.topbar .nav-link[aria-current="page"] .nav-link-icon,
.topbar .nav-link.is-active .nav-link-icon,
.header-settings.open,
.header-settings.open .header-settings-trigger,
.header-settings-trigger:hover,
.header-settings-trigger:focus-visible,
.header-settings-trigger[aria-expanded="true"] {
  transform: none !important;
}

.header-settings.open .header-settings-trigger-copy {
  transform: none !important;
}

.topbar .utility-chip:hover,
.topbar .utility-chip:focus-visible,
.topbar .btn-luxury:hover,
.topbar .btn-luxury:focus-visible,
.header-settings-trigger:hover,
.header-settings-trigger:focus-visible,
.header-settings-trigger[aria-expanded="true"],
.header-settings.open .header-settings-trigger {
  border-color: rgba(212, 175, 55, 0.34);
  box-shadow: 0 18px 36px rgba(15, 23, 42, 0.14);
}

body.dark-mode .topbar .utility-chip:hover,
body.dark-mode .topbar .utility-chip:focus-visible,
body.dark-mode .topbar .btn-luxury:hover,
body.dark-mode .topbar .btn-luxury:focus-visible,
body.dark-mode .header-settings-trigger:hover,
body.dark-mode .header-settings-trigger:focus-visible,
body.dark-mode .header-settings-trigger[aria-expanded="true"],
body.dark-mode .header-settings.open .header-settings-trigger {
  box-shadow: 0 18px 36px rgba(0, 0, 0, 0.24);
}

.topbar a:focus-visible,
.topbar button:focus-visible {
  outline: none;
  box-shadow: 0 0 0 3px rgba(15, 118, 110, 0.18), 0 18px 36px rgba(15, 23, 42, 0.14);
}

body.dark-mode .topbar a:focus-visible,
body.dark-mode .topbar button:focus-visible {
  box-shadow: 0 0 0 3px rgba(243, 216, 123, 0.18), 0 18px 36px rgba(0, 0, 0, 0.24);
}

.header-settings-panel {
  top: calc(100% + 0.72rem);
}

@media (max-width: 1439px) {
  .topbar .mx-auto > nav {
    margin-inline-start: clamp(0.8rem, 1.1vw, 1rem);
  }
}

@media (max-width: 1023px) {
  body,
  .topbar,
  .live-footer,
  .scroll-progress {
    padding-inline-end: 0;
  }

  .topbar .mx-auto > nav {
    margin-inline-start: 0;
  }
}

@media (prefers-reduced-motion: reduce) {
  .header-settings-panel.open,
  .status-dot {
    animation: none !important;
  }
}
`;

function hasSeenLoader() {
  try {
    return window.sessionStorage.getItem(SESSION_KEYS.loaderSeen) === "true";
  } catch {
    return false;
  }
}

function ensureRuntimeStyles() {
  if (document.head.querySelector("[data-runtime-tuning='true']")) return;
  const style = document.createElement("style");
  style.setAttribute("data-runtime-tuning", "true");
  style.textContent = RUNTIME_TUNING_STYLES;
  document.head.appendChild(style);
}

// عناصر التنقل المشتركة تغذي قائمة سطح المكتب ودرج الجوال وروابط الفوتر.
const NAV_ITEMS = [
  { key: "home", href: "index.html", label: { ar: "الرئيسية", en: "Home" } },
  { key: "about", href: "about.html", label: { ar: "من نحن", en: "About" } },
  { key: "facilities", href: "facilities.html", label: { ar: "المرافق", en: "Facilities" } },
  { key: "packages", href: "packages.html", label: { ar: "الباقات", en: "Packages" } },
  { key: "gallery", href: "gallery.html", label: { ar: "المعرض", en: "Gallery" } },
  { key: "faq", href: "faq.html", label: { ar: "الأسئلة الشائعة", en: "FAQ" } },
  { key: "contact", href: "contact.html", label: { ar: "اتصل بنا", en: "Contact" } },
];

// النصوص الثنائية المشتركة تغذي شاشة التحميل والهيكل والفوتر ولوحة الإعدادات.
const SHELL_TEXT = {
  brand: "Pet Paradise",
  tagline: { ar: "فندق فاخر للحيوانات الأليفة في العقبة", en: "Luxury Pet Hotel in Aqaba" },
  bookNow: { ar: "احجز الآن", en: "Book Now" },
  pages: { ar: "الصفحات", en: "Pages" },
  support: { ar: "الدعم", en: "Support" },
  supportEmail: { ar: "راسل الفريق", en: "Email the Team" },
  faqLink: { ar: "الأسئلة الشائعة", en: "Frequently Asked Questions" },
  visit: { ar: "معلومات الزيارة", en: "Visit Details" },
  hours: {
    ar: "يومياً من 8:00 صباحاً حتى 10:00 مساءً",
    en: "Daily from 8:00 AM to 10:00 PM",
  },
  address: CONTACT.location,
  footerCopy: {
    ar: "إقامة فاخرة للحيوانات الأليفة في العقبة مع أجنحة راقية، عناية احترافية، مساحات لعب آمنة، ومتابعة صحية موثوقة على مدار الساعة.",
    en: "Luxury pet stays in Aqaba with refined suites, professional grooming, safe play zones, and trusted wellness support around the clock.",
  },
  footerNote: {
    ar: "ضيافة راقية وهادئة تمنحك الثقة وتمنح حيوانك الأليف راحة حقيقية",
    en: "Refined, calm hospitality built for owner confidence and genuine pet comfort.",
  },
  footerLegal: {
    ar: "— جميع الحقوق محفوظة",
    en: "— All rights reserved",
  },
  logoAlt: { ar: "شعار بيت بارادايس", en: "Pet Paradise logo" },
  footerSubtitle: {
    ar: "ضيافة حديثة بمعايير رعاية عالمية",
    en: "Modern hospitality with world-class care standards",
  },
  openMenu: { ar: "فتح القائمة", en: "Open Menu" },
  closeMenu: { ar: "إغلاق القائمة", en: "Close Menu" },
  backToTop: { ar: "العودة إلى الأعلى", en: "Back to Top" },
  callUs: { ar: "اتصل بنا", en: "Call Us" },
  callShort: { ar: "اتصال", en: "Call" },
  emailUs: { ar: "راسلنا", en: "Email Us" },
  darkMode: { ar: "الوضع الداكن / الفاتح", en: "Dark / Light Mode" },
  smallerText: { ar: "تصغير النص", en: "Decrease Text Size" },
  largerText: { ar: "تكبير النص", en: "Increase Text Size" },
  settings: { ar: "إعدادات العرض", en: "Settings" },
  settingsHint: {
    ar: "تحكم بحجم النص والمظهر واللغة من زر واحد متكامل.",
    en: "Adjust theme, text size, and language from one compact panel.",
  },
  settingsMiniLabel: { ar: "المظهر واللغة", en: "Theme & Type" },
  settingsOpen: { ar: "فتح إعدادات العرض", en: "Open Display Settings" },
  settingsClose: { ar: "إغلاق إعدادات العرض", en: "Close Display Settings" },
  settingsThemeNote: {
    ar: "بدّل بين الوضعين الداكن والفاتح فوراً.",
    en: "Switch between dark and light instantly.",
  },
  settingsLargerNote: {
    ar: "نص أوضح للعناوين والمحتوى الرئيسي.",
    en: "Give headings and body copy more breathing room.",
  },
  settingsSmallerNote: {
    ar: "عرض أكثر إحكاماً مع الحفاظ على الوضوح.",
    en: "Tighten the layout while keeping the text readable.",
  },
  languageLabel: { ar: "اللغة", en: "Language" },
  languageSwitch: { ar: "English", en: "العربية" },
  languageNote: {
    ar: "التحويل إلى الإنجليزية مع تنسيق LTR أنيق.",
    en: "Switch back to Arabic with polished RTL layout.",
  },
  availableNow: { ar: "متاح الآن", en: "Available Now" },
  followUs: { ar: "تابعنا", en: "Follow Us" },
  loading: { ar: "تجهيز التجربة الفاخرة...", en: "Preparing the premium experience..." },
  loadingAria: { ar: "جاري تحميل الموقع", en: "Loading website" },
  officialOwner: { ar: "المالك الرسمي : محمد سكجها", en: "Official owner: Mohammad Sakajha" },
  supervisedBy: { ar: "بإشراف : م. احمد حمود", en: "Supervised by: Eng. Ahmad Hammoud" },
  followSite: { ar: "متابعة إلى الموقع", en: "Continue to site" },
  whatsapp: { ar: "واتساب", en: "WhatsApp" },
  whatsappAria: { ar: "تواصل معنا عبر واتساب", en: "Chat with us on WhatsApp" },
};

// نصوص التحقق وبيانات الباقات تدعم مسارات الحجز والتواصل.
const FORM_ERRORS = {
  required: { ar: "هذا الحقل مطلوب.", en: "This field is required." },
  email: { ar: "يرجى إدخال بريد إلكتروني صالح.", en: "Please enter a valid email address." },
  phone: { ar: "يرجى إدخال رقم هاتف صالح.", en: "Please enter a valid phone number." },
  checkout: {
    ar: "يجب أن يكون تاريخ المغادرة بعد تاريخ الوصول.",
    en: "Departure must be after the arrival date.",
  },
};

const PACKAGE_DETAILS = {
  daycare: {
    label: { ar: "الباقة الأساسية", en: "Basic Package" },
    price: 18,
    unit: { ar: "لليوم", en: "per day" },
    note: {
      ar: "مثالية للزيارات النهارية المنظمة مع اللعب الهادئ والإشراف والراحة القصيرة.",
      en: "Ideal for structured daytime visits with play, supervision, and short recovery breaks.",
    },
    perks: {
      ar: ["جلسات لعب داخلية وخارجية", "تحديث مختصر عند الاستلام", "إشراف يومي من الفريق"],
      en: ["Indoor and outdoor play sessions", "Pickup summary update", "Daily team supervision"],
    },
  },
  classic: {
    label: { ar: "الباقة الفاخرة", en: "Luxury Package" },
    price: 45,
    unit: { ar: "لليلة", en: "per night" },
    note: {
      ar: "إقامة متوازنة مع جناح خاص وتحديثات يومية ولمسات ضيافة أوضح.",
      en: "Balanced overnight care with a private suite, daily updates, and elevated hospitality.",
    },
    perks: {
      ar: ["جناح خاص للمبيت", "وجبات وروتين مخصص", "تحديث يومي بالصور"],
      en: ["Private overnight suite", "Tailored meals and routine", "Daily photo update"],
    },
  },
  premium: {
    label: { ar: "الباقة الملكية", en: "Royal Package" },
    price: 75,
    unit: { ar: "لليلة", en: "per night" },
    note: {
      ar: "أعلى مستوى من الرعاية مع سبا متكامل ومتابعة ذات أولوية وخدمة كونسيرج.",
      en: "Our highest tier with spa rituals, priority follow-up, and concierge-level care.",
    },
    perks: {
      ar: ["ترقية جناح فاخرة", "جلسة عناية احترافية", "أولوية في المتابعة الصحية"],
      en: ["Luxury suite upgrade", "Professional grooming session", "Priority wellness follow-up"],
    },
  },
};

const BOOKING_TEXT = {
  chooseArrival: { ar: "اختر تاريخ الوصول", en: "Select arrival date" },
  pickDateFirst: { ar: "حدد التاريخ أولاً", en: "Choose the date first" },
  singleDayVisit: { ar: "زيارة يومية واحدة", en: "One day visit" },
  selectStayDates: { ar: "حدد تاريخي الوصول والمغادرة", en: "Select arrival and departure dates" },
  nightSingular: { ar: "ليلة", en: "night" },
  nightPlural: { ar: "ليالٍ", en: "nights" },
  baseRate: { ar: "السعر الأساسي", en: "Base rate" },
  stayPlanPending: {
    ar: "اختر تاريخ الوصول أولاً، ثم حدد مدة الإقامة أو أدخل تاريخ المغادرة يدوياً.",
    en: "Pick an arrival date first, then choose a stay length or set departure manually.",
  },
  stayPlanAwaitingDeparture: {
    ar: "تم تحديد الوصول في",
    en: "Arrival selected for",
  },
  stayPlanChooseDeparture: {
    ar: "حدّد مدة الإقامة أو اختر المغادرة يدوياً.",
    en: "Choose a stay length or set departure manually.",
  },
};

// حالة الواجهة المركزية تقلل تكرار البحث في DOM وتحافظ على تزامن الطبقات العلوية.
const appState = {
  isInitialized: false,
  hasScrollListener: false,
  hasLifecycleListeners: false,
  hasImageOptimization: false,
  hasHeroParallax: false,
  hasHeroVideoControls: false,
  heroMedia: null,
  heroVideo: null,
  heroVideoObserver: null,
  lightbox: null,
  scrollProgress: null,
  scrollTopButton: null,
  loader: null,
  navSections: [],
  navSectionObserver: null,
  activeNavKey: "",
  currentYearNodes: [],
  lockedScrollY: 0,
  activeScrollLocks: new Set(),
  scrollRafId: 0,
  settingsOpen: false,
  resizeTimer: 0,
  language: DEFAULT_LANGUAGE,
};

// يتم تتبع حالة تنقل الجوال بشكل مستقل عن هيكل التطبيق العام.
const menuState = {
  isOpen: false,
  overlay: null,
  drawer: null,
};

const clamp = (value, min, max) => Math.min(Math.max(value, min), max);
const normalizeLanguage = (language) => (SUPPORTED_LANGUAGES.has(language) ? language : DEFAULT_LANGUAGE);

// أدوات التفضيلات الآمنة تبقي الواجهة مرنة عند تعذر التخزين.
function readStoredPreference(key, fallback = "") {
  try {
    const value = window.localStorage.getItem(key);
    return value ?? fallback;
  } catch {
    return fallback;
  }
}

function writeStoredPreference(key, value) {
  try {
    window.localStorage.setItem(key, value);
  } catch {
    // تجاهل فشل التخزين والحفاظ على عمل الواجهة.
  }
}

function getCurrentLanguage() {
  return normalizeLanguage(appState.language || root.getAttribute("lang") || DEFAULT_LANGUAGE);
}

function isRtlLanguage(language = getCurrentLanguage()) {
  return language === "ar";
}

function getDocumentDirection(language = getCurrentLanguage()) {
  return isRtlLanguage(language) ? "rtl" : "ltr";
}

function localize(content, language = getCurrentLanguage()) {
  if (content && typeof content === "object" && !Array.isArray(content)) {
    return content[language] ?? content[DEFAULT_LANGUAGE] ?? content.en ?? "";
  }
  return content;
}

function getShellText() {
  return Object.fromEntries(Object.entries(SHELL_TEXT).map(([key, value]) => [key, localize(value)]));
}

function sliderDotLabel(index) {
  return getCurrentLanguage() === "ar" ? `الانتقال إلى التقييم ${index + 1}` : `Go to review ${index + 1}`;
}

function getEventElement(event) {
  const rawTarget = event.target;
  if (rawTarget instanceof Element) return rawTarget;
  if (rawTarget && rawTarget.parentElement instanceof Element) return rawTarget.parentElement;
  return null;
}

function dedupeElements(selector) {
  const nodes = Array.from(document.querySelectorAll(selector));
  nodes.slice(1).forEach((node) => node.remove());
}

function normalizeShellDom() {
  dedupeElements("[data-site-loader]");
  dedupeElements("[data-mobile-overlay]");
  dedupeElements("[data-mobile-drawer]");
  dedupeElements("[data-scroll-progress]");
  dedupeElements("[data-scroll-top]");
  dedupeElements("[data-live-footer]");
}

function syncMenuElements() {
  menuState.overlay = document.querySelector("[data-mobile-overlay]");
  menuState.drawer = document.querySelector("[data-mobile-drawer]");
}

function getSettingsElements() {
  return {
    button: document.querySelector("[data-settings-toggle]"),
    panel: document.querySelector("[data-accessibility-panel]"),
    shell: document.querySelector("[data-accessibility-shell]"),
  };
}

function openSettings() {
  const { button, panel, shell } = getSettingsElements();
  if (!button || !panel || !shell || appState.settingsOpen) return;

  appState.settingsOpen = true;
  shell.classList.add("open");
  button.setAttribute("aria-expanded", "true");
  button.setAttribute("aria-label", localize(SHELL_TEXT.settingsClose));
  panel.classList.add("open");
  panel.setAttribute("aria-hidden", "false");
}

function closeSettings() {
  const { button, panel, shell } = getSettingsElements();
  if (!button || !panel || !shell) return;

  appState.settingsOpen = false;
  shell.classList.remove("open");
  button.setAttribute("aria-expanded", "false");
  button.setAttribute("aria-label", localize(SHELL_TEXT.settingsOpen));
  panel.classList.remove("open");
  panel.setAttribute("aria-hidden", "true");
}

function toggleSettings() {
  if (appState.settingsOpen) {
    closeSettings();
    return;
  }

  openSettings();
}

function iconMarkup(name, className = "icon-16") {
  const icons = {
    home: '<path d="M3 11.5 12 4l9 7.5"/><path d="M5 10.5V20h14v-9.5"/>',
    about: '<circle cx="12" cy="8" r="3.2"/><path d="M5 20a7 7 0 0 1 14 0"/>',
    facilities: '<path d="M4 21h16"/><path d="M6 21V8l6-3 6 3v13"/><path d="M10 11h4M10 14h4"/>',
    packages: '<path d="M12 3l7 4v10l-7 4-7-4V7l7-4Z"/><path d="m9.5 11 2 2 3-3"/>',
    gallery: '<rect x="4" y="5" width="16" height="14" rx="2"/><path d="m8 13 2.5-2.5L14 14l2-2 2 2"/><circle cx="9" cy="9" r="1"/>',
    faq: '<circle cx="12" cy="12" r="8"/><path d="M9.8 9.6a2.3 2.3 0 1 1 3.9 1.7c-.7.6-1.2 1-1.2 1.9"/><path d="M12 16.7h.01"/>',
    contact: '<path d="M5 8a2 2 0 0 1 2-2h10a2 2 0 0 1 2 2v8l-4-2-4 2-4-2-4 2V8Z"/>',
    chevron: '<path d="m9 6 6 6-6 6"/>',
    close: '<path d="m6 6 12 12M18 6 6 18"/>',
    menu: '<path d="M4 7h16M4 12h16M4 17h16"/>',
    moon: '<path d="M14.5 3.5a8.5 8.5 0 1 0 6 14.5A7 7 0 1 1 14.5 3.5Z"/>',
    sun: '<circle cx="12" cy="12" r="4"/><path d="M12 2v2.5M12 19.5V22M22 12h-2.5M4.5 12H2M19.1 4.9l-1.8 1.8M6.7 17.3l-1.8 1.8M19.1 19.1l-1.8-1.8M6.7 6.7 4.9 4.9"/>',
    textMinus: '<path d="M5 12h14"/>',
    textPlus: '<path d="M5 12h14"/><path d="M12 5v14"/>',
    book: '<rect x="4" y="5" width="16" height="15" rx="2"/><path d="M8 3v4M16 3v4M4 10h16"/>',
    call: '<path d="M6.5 4.5c.8-.8 2-.8 2.8 0l1.3 1.3c.7.7.8 1.8.2 2.7l-1 1.4a16 16 0 0 0 4.8 4.8l1.4-1c.9-.6 2-.5 2.7.2l1.3 1.3c.8.8.8 2 0 2.8l-1.1 1.1c-1.2 1.2-3 1.6-4.6.9-2.8-1.2-5.2-3-7.2-5s-3.8-4.4-5-7.2c-.7-1.6-.3-3.4.9-4.6l1.1-1.1Z"/>',
    clock: '<circle cx="12" cy="12" r="8"/><path d="M12 8v4l2.8 1.8"/>',
    mail: '<path d="M4 7h16v10H4z"/><path d="m4 8 8 6 8-6"/>',
    pin: '<path d="M12 21s5-4.9 5-9a5 5 0 1 0-10 0c0 4.1 5 9 5 9Z"/><circle cx="12" cy="12" r="1.8"/>',
    arrowUp: '<path d="M12 19V5"/><path d="m5 12 7-7 7 7"/>',
    globe:
      '<circle cx="12" cy="12" r="9"/><path d="M3 12h18"/><path d="M12 3a15 15 0 0 1 0 18"/><path d="M12 3a15 15 0 0 0 0 18"/>',
    settings:
      '<path d="M10.2 3.6h3.6l.6 2a6.9 6.9 0 0 1 1.7.9l2-.8 1.8 3.1-1.5 1.5a6.5 6.5 0 0 1 0 1.8l1.5 1.5-1.8 3.1-2-.8a6.9 6.9 0 0 1-1.7.9l-.6 2h-3.6l-.6-2a6.9 6.9 0 0 1-1.7-.9l-2 .8-1.8-3.1 1.5-1.5a6.5 6.5 0 0 1 0-1.8L4.1 8.8l1.8-3.1 2 .8a6.9 6.9 0 0 1 1.7-.9l.6-2Z"/><circle cx="12" cy="12" r="2.7"/>',
    instagram:
      '<rect x="4" y="4" width="16" height="16" rx="4"/><circle cx="12" cy="12" r="3.2"/><circle cx="17" cy="7" r="1"/>',
    facebook: '<path d="M14.5 8H16V4.5h-2c-2.5 0-4 1.5-4 4.2V11H8v3.2h2v5.3h3.6v-5.3H16L16.4 11h-2.8V9c0-.7.3-1 1-1Z"/>',
    whatsapp:
      '<path d="M12 20a8 8 0 1 0-4.2-1.2L4 20l1.3-3.6A8 8 0 0 0 12 20Z"/><path d="M9.4 8.8c.2-.4.4-.4.7-.4h.6c.2 0 .4 0 .5.4l.5 1.4c.1.2.1.4 0 .6l-.4.6a.6.6 0 0 0 0 .7c.4.7 1 1.4 1.7 1.8.2.1.5.1.7 0l.7-.4c.2-.1.4-.1.6 0l1.3.6c.4.2.4.3.4.6v.6c0 .3 0 .6-.3.8-.4.4-1 .6-1.7.5-1.1-.2-2.2-.8-3.3-1.8s-1.7-2.1-1.9-3.2c-.1-.7.1-1.3.5-1.8Z"/>',
  };

  if (!icons[name]) return "";
  return `<svg class="${className}" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">${icons[name]}</svg>`;
}

function navMarkup(currentPage, mobile = false) {
  return NAV_ITEMS.map(
    ({ key, href, label }) => `
      <a class="${mobile ? "mobile-link" : "nav-link"}" href="${href}"${
        currentPage === key ? ' aria-current="page"' : ""
      } data-nav-item="${key}">
        <span class="${mobile ? "mobile-link-main" : "nav-link-main"}">
          <span class="nav-link-icon">${iconMarkup(key, "icon-14")}</span>
          <span>${localize(label)}</span>
        </span>
        ${mobile ? `<span class="mobile-link-arrow">${iconMarkup("chevron", "icon-14")}</span>` : ""}
      </a>
    `
  ).join("");
}

// يتم رسم شاشة التحميل والترويسة والفوتر وشريط الإجراءات السريعة داخل أماكنها في الصفحة.
function renderShell() {
  const shell = document.querySelector("[data-site-shell]");
  const footer = document.querySelector("[data-site-footer]");
  const page = body.dataset.page || "home";
  const text = getShellText();
  const direction = getDocumentDirection();
  const skipLoader = false;

  if (shell) {
    shell.innerHTML = `
      <div class="site-loader${skipLoader ? " loaded" : ""}" data-site-loader data-skip="${skipLoader ? "true" : "false"}" aria-live="polite" aria-label="${text.loadingAria}">
        <div class="site-loader-panel">
          <span class="site-loader-glow" aria-hidden="true"></span>
          <span class="site-loader-logo">
            <img class="brand-logo" src="assets/images/branding/logo.png" alt="${text.logoAlt}" width="1077" height="847" decoding="async" fetchpriority="high">
          </span>
          <p class="site-loader-brand">${text.brand}</p>

          <!-- Premium inline-SVG credits (split roles + names for typography control) -->
          <div class="site-loader-credits" aria-label="Pet Paradise credits">
            <div class="credit-item" aria-hidden="false">
              <!-- Gold Crown SVG (minimalist) -->
              <svg class="credit-icon credit-icon--crown" viewBox="0 0 64 64" fill="none" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
                <path d="M14 24l8 10 6-20 6 20 8-10 6 30H8l6-30Z" stroke="rgba(255,215,0,0.95)" stroke-width="2" stroke-linejoin="round"/>
                <path d="M22 34l-4 6h28l-4-6" stroke="rgba(255,215,0,0.6)" stroke-width="2" stroke-linejoin="round"/>
                <path d="M18 54h28" stroke="rgba(255,215,0,0.55)" stroke-width="2" stroke-linecap="round"/>
              </svg>

              <span class="role">المالك الرسمي :</span>
              <span class="name">محمد سكجها</span>
            </div>

            <div class="credit-item" aria-hidden="false">
              <!-- Shield + Star SVG -->
              <svg class="credit-icon credit-icon--shield" viewBox="0 0 64 64" fill="none" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
                <path d="M32 6c10 7 18 6 22 8v18c0 16-10 24-22 26C20 56 10 48 10 32V14c4-2 12-1 22-8Z" stroke="rgba(255,215,0,0.92)" stroke-width="2" stroke-linejoin="round"/>
                <path d="M32 20l4 8 9 1-7 6 2 9-8-4-8 4 2-9-7-6 9-1 4-8Z" fill="rgba(255,215,0,0.18)" stroke="rgba(255,215,0,0.75)" stroke-width="1.5" stroke-linejoin="round"/>
              </svg>

              <span class="role">بإشراف : م. احمد حمود</span>
              <span class="name"> </span>
            </div>
          </div>

          <p class="site-loader-note">${text.loading}</p>


          <!-- Luxury pet cinematic animation (CSS-only, performance-friendly) -->
          <div class="site-loader-cinematic" aria-hidden="true">
            <span class="cinematic-orb orb-a"></span>
            <span class="cinematic-orb orb-b"></span>
            <span class="cinematic-orb orb-c"></span>
            <span class="cinematic-spark spark-a"></span>
            <span class="cinematic-spark spark-b"></span>

            <!-- Simple pet silhouette inspired loop -->
            <span class="cinematic-pet">
              <span class="pet-head"></span>
              <span class="pet-body"></span>
              <span class="pet-tail"></span>
              <span class="pet-ears"></span>
            </span>
          </div>

          <span class="loader-track" aria-hidden="true"><span></span></span>

          <button class="site-loader-cta" type="button" data-loader-continue>
            ${text.followSite ?? "متابعة إلى الموقع"}
          </button>
        </div>
      </div>
      <div class="mobile-overlay" data-mobile-overlay aria-hidden="true"></div>
      <div class="scroll-progress"><span data-scroll-progress></span></div>
      <aside class="mobile-drawer flex flex-col" data-mobile-drawer aria-hidden="true" data-direction="${direction}">
        <div class="mb-6 flex items-center justify-between gap-4">
          <div class="flex items-center gap-3">
            <span class="brand-mark">
              <img class="brand-logo" src="assets/images/branding/logo.png" alt="${text.logoAlt}" width="1077" height="847" decoding="async">
            </span>
            <div>
              <p class="font-display text-lg font-semibold">${text.brand}</p>
              <p class="text-sm text-stone-300">${text.tagline}</p>
            </div>
          </div>
          <button class="utility-chip text-sm text-charcoal" type="button" data-menu-close aria-label="${text.closeMenu}">
            ${iconMarkup("close", "icon-16")}
          </button>
        </div>
        <nav class="flex-1" aria-label="${text.pages}">${navMarkup(page, true)}
          <a class="mobile-link" href="book.html">
            <span class="mobile-link-main">
              <span class="nav-link-icon">${iconMarkup("book", "icon-14")}</span>
              <span>${text.bookNow}</span>
            </span>
            <span class="mobile-link-arrow">${iconMarkup("chevron", "icon-14")}</span>
          </a>
        </nav>
        <div class="mt-6 grid gap-3 text-sm text-stone-300">
          <a href="tel:${CONTACT.phoneHref}" class="mobile-support-link">
            <span class="nav-link-icon">${iconMarkup("call", "icon-14")}</span>
            <span>${text.callUs}</span>
          </a>
          <a href="mailto:${CONTACT.email}" class="mobile-support-link">
            <span class="nav-link-icon">${iconMarkup("mail", "icon-14")}</span>
            <span>${text.emailUs}</span>
          </a>
        </div>
      </aside>
      <header class="topbar fixed inset-x-0 top-0 z-40">
        <div class="mx-auto flex max-w-7xl items-center justify-between gap-4 px-4 py-4 sm:px-6 lg:px-8">
          <a href="index.html" class="header-brand flex items-center gap-3">
            <span class="brand-mark">
              <img class="brand-logo" src="assets/images/branding/logo.png" alt="${text.logoAlt}" width="1077" height="847" decoding="async" fetchpriority="high">
            </span>
            <div class="header-brand-copy">
              <p class="header-brand-title font-display text-lg font-semibold text-slate-900">${text.brand}</p>
              <p class="header-tagline text-xs text-slate-500">${text.tagline}</p>
            </div>
          </a>
          <nav class="hidden items-center gap-8 xl:flex" aria-label="${text.pages}">${navMarkup(page)}</nav>
          <div class="header-actions">
            <div class="header-settings" data-accessibility-shell>
              <button class="header-settings-trigger" type="button" data-settings-toggle aria-expanded="false" aria-controls="site-accessibility-panel" aria-label="${text.settingsOpen}">
                <span class="header-settings-trigger-icon">${iconMarkup("settings", "icon-16")}</span>
                <span class="header-settings-trigger-copy">
                  <span class="header-settings-trigger-label">${text.settings}</span>
                  <span class="header-settings-trigger-note">${text.settingsMiniLabel}</span>
                </span>
              </button>
              <div class="accessibility-panel section-frame header-settings-panel" id="site-accessibility-panel" data-accessibility-panel aria-hidden="true">
                <div class="accessibility-panel-header">
                  <p class="accessibility-panel-title">${text.settings}</p>
                  <p class="accessibility-panel-note">${text.settingsHint}</p>
                </div>
                <div class="accessibility-panel-actions">
                  <button class="settings-option" type="button" data-font="increase" aria-label="${text.largerText}">
                    <span class="settings-option-copy">
                      <span class="settings-option-label">${text.largerText}</span>
                      <span class="settings-option-note">${text.settingsLargerNote}</span>
                    </span>
                    <span class="settings-option-icon">${iconMarkup("textPlus", "icon-18")}</span>
                  </button>
                  <button class="settings-option" type="button" data-font="decrease" aria-label="${text.smallerText}">
                    <span class="settings-option-copy">
                      <span class="settings-option-label">${text.smallerText}</span>
                      <span class="settings-option-note">${text.settingsSmallerNote}</span>
                    </span>
                    <span class="settings-option-icon">${iconMarkup("textMinus", "icon-18")}</span>
                  </button>
                  <button class="settings-option" type="button" data-theme-toggle aria-label="${text.darkMode}">
                    <span class="settings-option-copy">
                      <span class="settings-option-label">${text.darkMode}</span>
                      <span class="settings-option-note">${text.settingsThemeNote}</span>
                    </span>
                    <span class="settings-option-icon" data-theme-icon>${iconMarkup("moon", "icon-18")}</span>
                  </button>
                  <button class="settings-option" type="button" data-language-toggle aria-label="${text.languageLabel}">
                    <span class="settings-option-copy">
                      <span class="settings-option-label">${text.languageSwitch}</span>
                      <span class="settings-option-note">${text.languageNote}</span>
                    </span>
                    <span class="settings-option-icon">${iconMarkup("globe", "icon-18")}</span>
                  </button>
                </div>
              </div>
            </div>
            <a href="book.html" class="btn-luxury btn-primary header-book-btn hidden md:inline-flex">
              <span class="header-book-btn-icon" aria-hidden="true">${iconMarkup("book", "icon-16")}</span>
              <span class="header-book-btn-label">${text.bookNow}</span>
            </a>
            <button class="utility-chip top-action xl:hidden" type="button" data-menu-toggle aria-label="${text.openMenu}">
              ${iconMarkup("menu", "icon-16")}
            </button>
          </div>
        </div>
      </header>
      <button class="scroll-top" type="button" data-scroll-top aria-label="${text.backToTop}">
        ${iconMarkup("arrowUp", "icon-16")}
      </button>

      <div class="live-footer" data-live-footer>


        <span class="live-footer-status">
          <span class="live-footer-status-icon" aria-hidden="true">
            <span class="status-dot"></span>
          </span>
          <span class="live-footer-status-label">${text.availableNow}</span>
        </span>
        <a href="tel:${CONTACT.phoneHref}" class="live-footer-btn">
          <span class="live-footer-btn-icon" aria-hidden="true">${iconMarkup("call", "icon-16")}</span>
          <span class="live-footer-btn-label">${text.callShort}</span>
        </a>
        <a href="book.html" class="live-footer-btn live-footer-btn--primary">
          <span class="live-footer-btn-icon" aria-hidden="true">${iconMarkup("book", "icon-16")}</span>
          <span class="live-footer-btn-label">${text.bookNow}</span>
        </a>
      </div>
    `;
  }

  if (footer) {
    footer.innerHTML = `
      <footer class="page-section px-4 pb-12 sm:px-6 lg:px-8">
        <div class="mx-auto max-w-7xl footer-premium">
          <div class="footer-grid">
            <div>
              <span class="footer-kicker">${text.tagline}</span>
              <div class="mt-4 flex items-center gap-3">
                <span class="brand-mark footer-brand-mark">
                  <img class="brand-logo" src="assets/images/branding/logo.png" alt="${text.logoAlt}" width="1077" height="847" decoding="async" loading="lazy" fetchpriority="low">
                </span>
                <div>
                  <p class="font-display text-xl font-semibold text-white">${text.brand}</p>
                  <p class="footer-subtitle">${text.footerSubtitle}</p>
                </div>
              </div>
              <p class="footer-copy">${text.footerCopy}</p>
              <div class="footer-social-row">
                <span class="footer-title">${text.followUs}</span>
                <div class="footer-socials">
                  <a class="footer-social" href="https://www.instagram.com" target="_blank" rel="noreferrer" aria-label="Instagram">
                    ${iconMarkup("instagram", "icon-16")}
                  </a>
                  <a class="footer-social" href="https://www.facebook.com" target="_blank" rel="noreferrer" aria-label="Facebook">
                    ${iconMarkup("facebook", "icon-16")}
                  </a>
                  <a class="footer-social" href="${CONTACT.whatsappHref}" target="_blank" rel="noreferrer" aria-label="WhatsApp">
                    ${iconMarkup("whatsapp", "icon-16")}
                  </a>
                </div>
              </div>
            </div>
            <div>
              <p class="footer-title">${text.pages}</p>
              <div class="footer-link-list">
                ${NAV_ITEMS.map(({ href, label }) => `<a class="footer-link" href="${href}">${localize(label)}</a>`).join("")}
              </div>
            </div>
            <div>
              <p class="footer-title">${text.support}</p>
              <div class="footer-link-list">
                <a class="footer-link" href="book.html">${text.bookNow}</a>
                <a class="footer-link" href="contact.html">${text.supportEmail}</a>
                <a class="footer-link" href="faq.html">${text.faqLink}</a>
              </div>
            </div>
            <div>
              <p class="footer-title">${text.visit}</p>
              <div class="footer-contact-list">
                <div class="footer-contact-item">
                  <span class="footer-contact-icon">${iconMarkup("clock", "icon-16")}</span>
                  <p>${text.hours}</p>
                </div>
                <div class="footer-contact-item">
                  <span class="footer-contact-icon">${iconMarkup("mail", "icon-16")}</span>
                  <a class="footer-link" href="mailto:${CONTACT.email}" dir="ltr">${CONTACT.email}</a>
                </div>
                <div class="footer-contact-item">
                  <span class="footer-contact-icon">${iconMarkup("call", "icon-16")}</span>
                  <a class="footer-link footer-phone" href="tel:${CONTACT.phoneHref}" dir="ltr">${CONTACT.phoneDisplay}</a>
                </div>
                <div class="footer-contact-item">
                  <span class="footer-contact-icon">${iconMarkup("pin", "icon-16")}</span>
                  <p>${text.address}</p>
                </div>
              </div>
            </div>
          </div>
          <div class="footer-bottom">
            <p class="footer-bottom-note">${text.footerNote}</p>
            <p class="footer-bottom-legal">&copy; <span data-current-year></span> ${text.brand} ${text.footerLegal}</p>
          </div>
        </div>
      </footer>
    `;
  }
}

// تطبيق النصوص المترجمة والـ placeholders والنص البديل ووسوم ARIA على كامل المستند.
function syncDocumentLanguage(language) {
  const nextLanguage = normalizeLanguage(language);
  appState.language = nextLanguage;
  root.setAttribute("lang", nextLanguage);
  root.setAttribute("dir", getDocumentDirection(nextLanguage));
  body.classList.toggle("rtl", isRtlLanguage(nextLanguage));
  body.classList.toggle("ltr", !isRtlLanguage(nextLanguage));
  body.dataset.language = nextLanguage;
}

function applyLocalizedContent() {
  const language = getCurrentLanguage();
  const textKey = language;
  const htmlKey = `${language}Html`;
  const placeholderKey = `${language}Placeholder`;
  const altKey = `${language}Alt`;
  const ariaKey = `${language}Aria`;

  document.querySelectorAll("[data-ar], [data-en]").forEach((node) => {
    const value = node.dataset[textKey];
    if (typeof value === "string") node.textContent = value;
  });

  document.querySelectorAll("[data-ar-html], [data-en-html]").forEach((node) => {
    const value = node.dataset[htmlKey];
    if (typeof value === "string") node.innerHTML = value;
  });

  document.querySelectorAll("[data-ar-placeholder], [data-en-placeholder]").forEach((node) => {
    const value = node.dataset[placeholderKey];
    if (typeof value === "string") node.placeholder = value;
  });

  document.querySelectorAll("[data-ar-alt], [data-en-alt]").forEach((node) => {
    const value = node.dataset[altKey];
    if (typeof value === "string") node.alt = value;
  });

  document.querySelectorAll("[data-ar-aria], [data-en-aria]").forEach((node) => {
    const value = node.dataset[ariaKey];
    if (typeof value === "string") node.setAttribute("aria-label", value);
  });

  document.querySelectorAll("[data-slider-dots] .testimonial-dot").forEach((dot, index) => {
    dot.setAttribute("aria-label", sliderDotLabel(index));
  });
}

function applyLanguage(language, { persist = true } = {}) {
  const nextLanguage = normalizeLanguage(language);
  resetMenuState();
  appState.settingsOpen = false;
  syncDocumentLanguage(nextLanguage);
  if (persist) writeStoredPreference(STORAGE_KEYS.language, nextLanguage);
  renderShell();
  cacheUi();
  initLoader();
  appState.hasImageOptimization = false;
  optimizeImages();
  applyLocalizedContent();
  setCurrentYear();
  initScrollTop();
  initMenu();
  initActiveSectionNav();
  applyTheme(readStoredPreference(STORAGE_KEYS.theme, "dark"));
  syncScrollState();
  const bookingForm = document.querySelector("[data-booking-form]");
  if (bookingForm) updateBookingSummary(bookingForm);
}

// تخزين عقد الهيكل المستخدمة بكثرة بعد رسم العلامات المشتركة.
function cacheUi() {
  normalizeShellDom();
  syncMenuElements();
  appState.currentYearNodes = document.querySelectorAll("[data-current-year]");
  appState.lightbox = document.querySelector("[data-lightbox-modal]");
  appState.scrollProgress = document.querySelector("[data-scroll-progress]");
  appState.scrollTopButton = document.querySelector("[data-scroll-top]");
  appState.loader = document.querySelector("[data-site-loader]");
}

function syncScrollState() {
  body.classList.toggle("scrolled", window.scrollY > 16);
  if (appState.scrollTopButton) {
    appState.scrollTopButton.classList.toggle("visible", window.scrollY > 360);
  }
  if (appState.scrollProgress) {
    const max = document.documentElement.scrollHeight - window.innerHeight;
    const progress = max > 0 ? (window.scrollY / max) * 100 : 0;
    appState.scrollProgress.style.width = `${progress}%`;
  }
  syncHeroParallax();
  if (!appState.navSectionObserver) syncActiveSectionNav();
}

// قفل التمرير يحافظ على محاذاة الواجهة الثابتة عند فتح القوائم أو نافذة المعرض.
function setScrollbarCompensation(value = 0) {
  root.style.setProperty("--scrollbar-compensation", `${Math.max(0, value)}px`);
}

function lockScroll(reason) {
  if (appState.activeScrollLocks.has(reason)) return;
  if (appState.activeScrollLocks.size === 0) {
    appState.lockedScrollY = window.scrollY || window.pageYOffset || 0;
    setScrollbarCompensation(window.innerWidth - document.documentElement.clientWidth);
    body.style.overflow = "hidden";
    body.style.position = "fixed";
    body.style.top = `-${appState.lockedScrollY}px`;
    body.style.left = "0";
    body.style.right = "0";
    body.style.width = "100%";
  }
  appState.activeScrollLocks.add(reason);
  body.classList.add("menu-open");
}

function unlockScroll(reason) {
  if (!appState.activeScrollLocks.has(reason)) return;
  appState.activeScrollLocks.delete(reason);
  if (appState.activeScrollLocks.size > 0) return;
  body.classList.remove("menu-open");
  body.style.overflow = "";
  body.style.position = "";
  body.style.top = "";
  body.style.left = "";
  body.style.right = "";
  body.style.width = "";
  setScrollbarCompensation(0);
  window.scrollTo({ top: appState.lockedScrollY, left: 0, behavior: "auto" });
}

function forceRestoreScroll() {
  appState.activeScrollLocks.clear();
  body.classList.remove("menu-open");
  body.style.overflow = "";
  body.style.position = "";
  body.style.top = "";
  body.style.left = "";
  body.style.right = "";
  body.style.width = "";
  setScrollbarCompensation(0);
}

// عناصر قائمة الجوال تعيد استخدام منطق قفل التمرير نفسه المستخدم في باقي الطبقات العلوية.
function openMenu() {
  syncMenuElements();
  if (!menuState.drawer || !menuState.overlay || menuState.isOpen) return;
  menuState.isOpen = true;
  closeSettings();
  menuState.drawer.setAttribute("data-direction", root.dir);
  lockScroll("menu");
  menuState.overlay.classList.add("open");
  menuState.drawer.classList.add("open");
  menuState.overlay.setAttribute("aria-hidden", "false");
  menuState.drawer.setAttribute("aria-hidden", "false");
}

function closeMenu() {
  syncMenuElements();
  if (!menuState.drawer || !menuState.overlay) return;
  menuState.isOpen = false;
  unlockScroll("menu");
  menuState.overlay.classList.remove("open");
  menuState.drawer.classList.remove("open");
  menuState.overlay.setAttribute("aria-hidden", "true");
  menuState.drawer.setAttribute("aria-hidden", "true");
}

function toggleMenu() {
  if (menuState.isOpen) {
    closeMenu();
    return;
  }
  openMenu();
}

function resetMenuState() {
  syncMenuElements();
  menuState.isOpen = false;
  if (menuState.overlay) {
    menuState.overlay.classList.remove("open");
    menuState.overlay.setAttribute("aria-hidden", "true");
  }
  if (menuState.drawer) {
    menuState.drawer.classList.remove("open");
    menuState.drawer.setAttribute("aria-hidden", "true");
  }
  forceRestoreScroll();
}

// يتم تطبيق تفضيلات الثيم وحجم الخط عالميًا عبر متغيرات CSS والكلاسات.
function applyTheme(theme) {
  const dark = theme !== "light";
  body.classList.toggle("dark-mode", dark);
  body.classList.toggle("light-mode", !dark);
  writeStoredPreference(STORAGE_KEYS.theme, dark ? "dark" : "light");
  document.querySelectorAll("[data-theme-icon]").forEach((node) => {
    node.innerHTML = iconMarkup(dark ? "sun" : "moon", "icon-16");
  });
}

function applyFontScale(scale) {
  const safeScale = clamp(scale, 0.95, 1.12);
  root.style.setProperty("--font-scale", safeScale.toFixed(2));
  writeStoredPreference(STORAGE_KEYS.fontScale, safeScale.toFixed(2));
}

function setCurrentYear() {
  const year = new Date().getFullYear();
  appState.currentYearNodes.forEach((node) => {
    node.textContent = year;
  });
}

// شاشة التحميل تختفي بسلاسة في أول زيارة وتتجاوز الزيارات المتكررة ضمن الجلسة.
function initLoader() {
  if (!appState.loader) return;
  const loader = appState.loader;
  const skipLoader = loader.dataset.skip === "true";
  let settled = false;

  const hideLoader = () => {
    if (settled) return;
    settled = true;
    loader.classList.add("loaded");
    window.setTimeout(() => {
      if (loader.parentElement) loader.remove();
    }, 820);
  };

  // One-time visibility logic (session scoped):
  // Loader only appears when `sessionStorage.getItem('loader-shown')` is null.
  try {
    const seen = window.sessionStorage.getItem(SESSION_KEYS.loaderShown) === "true";
    if (seen) {
      window.requestAnimationFrame(hideLoader);
      return;
    }
  } catch {
    // If sessionStorage fails, keep the loader behavior functional.
  }

// إذا كنت لا تريد أي شاشة تحميل عند فتح الموقع، نخلي initLoader يقفلها فورًا.
  // هذا يتوافق مع طلب: الدخول للصفحة الرئيسية مباشرة بدون عرض عبارة المالك/الإشراف.
  const cta = loader.querySelector("[data-loader-continue]");
  if (cta && !cta.dataset.bound) {
    cta.dataset.bound = "true";
    cta.addEventListener(
      "click",
      (event) => {
        event?.preventDefault?.();
        event?.stopPropagation?.();
        try {
          window.sessionStorage.setItem(SESSION_KEYS.loaderShown, "true");
        } catch {
          // ignore
        }
        hideLoader();
      },
      { once: true }
    );
  }

  // إخفاء اللودر فوراً عند أول دخول.
  try {
    window.sessionStorage.setItem(SESSION_KEYS.loaderShown, "true");
  } catch {
    // ignore
  }
  window.requestAnimationFrame(hideLoader);
}


// توحيد تلميحات تحميل الصور حتى تبقى وسائط الواجهة الرئيسية ذات أولوية وتُحمّل الوسائط الأدنى بشكل lazy.
function optimizeImages() {
  if (appState.hasImageOptimization) return;

  const heroImage = document.querySelector(".hero-asset, .page-banner img");
  if (heroImage) {
    heroImage.setAttribute("loading", "eager");
    heroImage.setAttribute("fetchpriority", "high");
    heroImage.setAttribute("decoding", "async");
  }

  document.querySelectorAll("img").forEach((image) => {
    if (!image.hasAttribute("decoding")) image.setAttribute("decoding", "async");
    const isPriority = image === heroImage || Boolean(image.closest(".topbar, [data-site-loader]"));
    if (!isPriority && !image.hasAttribute("loading")) image.setAttribute("loading", "lazy");
    if (!isPriority && !image.hasAttribute("fetchpriority")) image.setAttribute("fetchpriority", "low");
    image.draggable = false;
  });

  appState.hasImageOptimization = true;
}

// تمييز رابط التنقل النشط يتبع الأقسام الظاهرة في الصفحة الرئيسية والصفحات الداخلية.
function setActiveNavLink(key) {
  if (!key) return;
  let matched = false;
  document.querySelectorAll("[data-nav-item]").forEach((link) => {
    const active = link.dataset.navItem === key;
    link.classList.toggle("is-active", active);
    if (active) {
      link.setAttribute("aria-current", "page");
      matched = true;
    } else if (link.getAttribute("aria-current") === "page") {
      link.removeAttribute("aria-current");
    }
  });

  if (matched) appState.activeNavKey = key;
}

function buildNavSections() {
  const explicit = Array.from(document.querySelectorAll("[data-nav-section]"));
  if (explicit.length) {
    return explicit
      .map((section) => ({ section, key: section.dataset.navSection || "" }))
      .filter(({ key }) => Boolean(key && document.querySelector(`[data-nav-item="${key}"]`)));
  }

  const pageSections = Array.from(document.querySelectorAll("main > section.page-section"));
  if (!pageSections.length) return [];

  const page = body.dataset.page || "home";
  if (page === "home") {
    const homeMap = ["home", "about", "about", "facilities", "packages", "gallery", "contact"];
    return pageSections
      .map((section, index) => ({ section, key: homeMap[index] || "home" }))
      .filter(({ key }) => Boolean(document.querySelector(`[data-nav-item="${key}"]`)));
  }

  if (!document.querySelector(`[data-nav-item="${page}"]`)) return [];
  return pageSections.map((section) => ({ section, key: page }));
}

function syncActiveSectionNav() {
  if (!appState.navSections.length) return;

  const marker = window.innerHeight * 0.34;
  let bestKey = appState.navSections[0].key;
  let bestDistance = Number.POSITIVE_INFINITY;

  appState.navSections.forEach(({ section, key }) => {
    const rect = section.getBoundingClientRect();
    const inRange = rect.bottom > marker * 0.45 && rect.top < window.innerHeight * 0.78;
    if (!inRange) return;
    const distance = Math.abs(rect.top - marker);
    if (distance < bestDistance) {
      bestDistance = distance;
      bestKey = key;
    }
  });

  if (bestKey && bestKey !== appState.activeNavKey) {
    setActiveNavLink(bestKey);
  }
}

function initActiveSectionNav() {
  appState.navSections = buildNavSections();
  const initialKey = body.dataset.page || "home";
  setActiveNavLink(initialKey);
  appState.navSectionObserver?.disconnect();
  appState.navSectionObserver = null;
  if ("IntersectionObserver" in window && appState.navSections.length > 1) {
    appState.navSectionObserver = new IntersectionObserver(
      () => {
        syncActiveSectionNav();
      },
      {
        threshold: [0.12, 0.32, 0.56],
        rootMargin: "-16% 0px -48% 0px",
      }
    );
    appState.navSections.forEach(({ section }) => appState.navSectionObserver?.observe(section));
  }
  syncActiveSectionNav();
}

// أدوات العدادات تدعم القيم التي تتضمن رموزًا أو سوابق أو نسبًا مئوية.
function parseCounterPattern(rawValue) {
  const valueText = (rawValue || "").trim();
  if (!valueText) return null;
  const match = valueText.match(/([+-]?)(\d[\d,]*\.?\d*)/);
  if (!match) return null;

  const numericValue = Number.parseFloat(match[2].replace(/,/g, ""));
  if (!Number.isFinite(numericValue)) return null;

  const matchIndex = match.index || 0;
  const prefix = `${valueText.slice(0, matchIndex)}${match[1] === "+" ? "+" : ""}`;
  const suffix = valueText.slice(matchIndex + match[0].length);
  const decimals = (match[2].split(".")[1] || "").length;

  return { numericValue, prefix, suffix, decimals };
}

function animateCounter(node) {
  if (!node || node.dataset.counterDone === "true") return;
  const parsed = parseCounterPattern(node.dataset.counterTarget || node.textContent);
  if (!parsed) return;

  const { numericValue, prefix, suffix, decimals } = parsed;
  const duration = 1200;
  const startedAt = performance.now();

  const format = (value) => {
    if (decimals > 0) return `${value.toFixed(decimals)}`;
    return `${Math.round(value).toLocaleString("en-US")}`;
  };

  const step = (now) => {
    const progress = clamp((now - startedAt) / duration, 0, 1);
    const eased = 1 - Math.pow(1 - progress, 3);
    const current = numericValue * eased;
    node.textContent = `${prefix}${format(current)}${suffix}`;
    if (progress < 1) {
      window.requestAnimationFrame(step);
      return;
    }
    node.textContent = `${prefix}${format(numericValue)}${suffix}`;
    node.dataset.counterDone = "true";
  };

  window.requestAnimationFrame(step);
}

function initStatCounters() {
  const counters = Array.from(document.querySelectorAll("[data-hero-stats] .stat-value, [data-counter]"));
  if (!counters.length) return;

  counters.forEach((counter) => {
    if (!counter.dataset.counterTarget) {
      counter.dataset.counterTarget = counter.textContent.trim();
    }
    counter.textContent = "0";
  });

  if (!("IntersectionObserver" in window)) {
    counters.forEach((counter) => animateCounter(counter));
    return;
  }

  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (!entry.isIntersecting) return;
        animateCounter(entry.target);
        observer.unobserve(entry.target);
      });
    },
    { threshold: 0.45 }
  );

  counters.forEach((counter) => observer.observe(counter));
}

// أدوات الإظهار تحرك المحتوى غير الرئيسي تدريجيًا عند دخوله إلى منطقة العرض.
function markRevealTargets() {
  const selectors = [
    ".info-card",
    ".facility-card",
    ".package-card",
    ".gallery-card",
    ".luxury-gallery-card",
    ".why-card",
    ".faq-mini-card",
    ".cta-proof-card",
    ".page-section h2",
  ];

  const heroScopes = ".hero, .page-banner";
  const nodes = new Set();
  selectors.forEach((selector) => {
    document.querySelectorAll(selector).forEach((node) => {
      if (node.closest(heroScopes)) return;
      if (node.closest("[data-no-reveal]")) return;
      nodes.add(node);
    });
  });

  nodes.forEach((node) => node.classList.add("reveal"));
}

function initIntersectionReveals() {
  const nodes = Array.from(document.querySelectorAll(".reveal"));
  if (!nodes.length) return;

  if (!("IntersectionObserver" in window)) {
    nodes.forEach((node) => node.classList.add("reveal-visible"));
    return;
  }

  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (!entry.isIntersecting) return;
        entry.target.classList.add("reveal-visible");
        observer.unobserve(entry.target);
      });
    },
    { threshold: 0.16, rootMargin: "0px 0px -6% 0px" }
  );

  nodes.forEach((node) => {
    if (node.closest("[data-no-reveal]")) {
      node.classList.add("reveal-visible");
      return;
    }
    observer.observe(node);
  });
}

// تأثيرات الواجهة الرئيسية تُفعّل فقط على الأجهزة القادرة حفاظًا على الأداء في العتاد الأضعف.
function initHeroParallax() {
  appState.heroMedia = document.querySelector(".hero-asset, .hero-video");
  if (!appState.heroMedia) return;
  if (
    window.matchMedia("(prefers-reduced-motion: reduce)").matches ||
    !window.matchMedia("(min-width: 1024px) and (hover: hover) and (pointer: fine)").matches
  ) {
    appState.hasHeroParallax = false;
    appState.heroMedia.style.transform = "";
    return;
  }
  syncHeroParallax();
  appState.hasHeroParallax = true;
}

function syncHeroParallax() {
  if (!appState.heroMedia) return;
  if (
    window.matchMedia("(prefers-reduced-motion: reduce)").matches ||
    !window.matchMedia("(min-width: 1024px) and (hover: hover) and (pointer: fine)").matches
  ) {
    appState.hasHeroParallax = false;
    appState.heroMedia.style.transform = "";
    return;
  }
  const offset = Math.min(window.scrollY * 0.05, 56);
  appState.heroMedia.style.transform = `scale(1.04) translate3d(0, ${offset.toFixed(2)}px, 0)`;
}

function initHeroVideoPlayback() {
  if (appState.hasHeroVideoControls) return;

  const heroVideo = document.querySelector(".hero-video");
  if (!heroVideo) return;

  const heroSection = heroVideo.closest(".hero");
  if (!heroSection) return;

  appState.heroVideo = heroVideo;

  const syncPlayback = (isVisible) => {
    if (!appState.heroVideo) return;
    const shouldPause = document.hidden || !isVisible || window.matchMedia("(prefers-reduced-motion: reduce)").matches;
    if (shouldPause) {
      appState.heroVideo.pause();
      return;
    }

    const playPromise = appState.heroVideo.play();
    if (playPromise && typeof playPromise.catch === "function") {
      playPromise.catch(() => {});
    }
  };

  if ("IntersectionObserver" in window) {
    appState.heroVideoObserver = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          syncPlayback(entry.isIntersecting);
        });
      },
      { threshold: 0.2 }
    );
    appState.heroVideoObserver.observe(heroSection);
  }

  document.addEventListener("visibilitychange", () => {
    const rect = heroSection.getBoundingClientRect();
    const isVisible = rect.bottom > window.innerHeight * 0.18 && rect.top < window.innerHeight * 0.82;
    syncPlayback(isVisible);
  });

  appState.hasHeroVideoControls = true;
}

function initScrollTop() {
  appState.scrollTopButton?.addEventListener("click", () => {
    window.scrollTo({ top: 0, behavior: "smooth" });
  });
}

// التحقق المشترك للنماذج يحافظ على تزامن الحاويات المنسقة ونصوص الأخطاء.
function getFieldVisualTarget(field) {
  return field?.closest(".date-input-shell") || field;
}

function getFieldErrorNode(field) {
  return field?.closest(".field-shell")?.querySelector(".error-text") || null;
}

function validateField(field) {
  if (!field) return true;
  const errorNode = getFieldErrorNode(field);
  const visualTarget = getFieldVisualTarget(field);
  let valid = true;
  let message = "";

  if (field.required && !field.value.trim()) {
    valid = false;
    message = localize(FORM_ERRORS.required);
  }

  if (valid && field.type === "email" && field.value.trim()) {
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(field.value)) {
      valid = false;
      message = localize(FORM_ERRORS.email);
    }
  }

  if (valid && field.type === "tel" && field.value.trim()) {
    if (!/^[+0-9\s()-]{7,}$/.test(field.value)) {
      valid = false;
      message = localize(FORM_ERRORS.phone);
    }
  }

  visualTarget?.classList.toggle("field-error", !valid);
  if (visualTarget !== field) {
    field.classList.remove("field-error");
  }
  if (errorNode) errorNode.textContent = message;
  return valid;
}

// أغلفة حقول التاريخ المنسقة تفتح أداة الاختيار الأصلية لتسريع مسار الحجز.
function initDateInputShells(scope = document) {
  scope.querySelectorAll(".date-input-shell").forEach((shell) => {
    if (shell.dataset.dateShellBound === "true") return;
    const input = shell.querySelector("input[type='date']");
    if (!input) return;

    shell.addEventListener("click", (event) => {
      if (event.target instanceof HTMLElement && event.target.closest("input")) return;
      if (typeof input.showPicker === "function") {
        try {
          input.showPicker();
          return;
        } catch (_error) {
          // الرجوع إلى التركيز على الحقل عند عدم توفر واجهة اختيار التاريخ.
        }
      }
      input.focus();
    });

    shell.dataset.dateShellBound = "true";
  });
}

function setMinimumDates(form) {
  const today = new Date().toISOString().split("T")[0];
  const checkIn = form.querySelector("[name='checkin']");
  const checkOut = form.querySelector("[name='checkout']");
  if (!checkIn || !checkOut) return;

  checkIn.min = today;
  checkOut.min = checkIn.value || today;

  checkIn.addEventListener("change", () => {
    checkOut.min = checkIn.value || today;
    if (checkOut.value && checkOut.value <= checkIn.value) {
      checkOut.value = "";
    }
    updateBookingSummary(form);
  });

  checkOut.addEventListener("change", () => updateBookingSummary(form));
}

// أدوات الحجز المساعدة تنسق الباقات والتواريخ والأسعار داخل لوحة الملخص.
function getPackageDetails(packageKey) {
  return PACKAGE_DETAILS[packageKey] || PACKAGE_DETAILS.classic;
}

function formatCurrency(value) {
  return new Intl.NumberFormat(getCurrentLanguage() === "ar" ? "ar-JO" : "en-JO", {
    style: "currency",
    currency: "JOD",
    maximumFractionDigits: 0,
  }).format(value);
}

function parseDateValue(value) {
  if (!value || !/^\d{4}-\d{2}-\d{2}$/.test(value)) return null;
  const [year, month, day] = value.split("-").map(Number);
  const parsed = new Date(year, month - 1, day, 12, 0, 0, 0);
  return Number.isNaN(parsed.getTime()) ? null : parsed;
}

function formatDateValue(date) {
  if (!(date instanceof Date) || Number.isNaN(date.getTime())) return "";
  const year = date.getFullYear();
  const month = `${date.getMonth() + 1}`.padStart(2, "0");
  const day = `${date.getDate()}`.padStart(2, "0");
  return `${year}-${month}-${day}`;
}

function formatDisplayDate(value) {
  const parsed = parseDateValue(value);
  if (!parsed) return value;
  return new Intl.DateTimeFormat(getCurrentLanguage() === "ar" ? "ar-SA" : "en-US", {
    day: "numeric",
    month: "short",
    year: "numeric",
  }).format(parsed);
}

function calculateNights(checkInValue, checkOutValue) {
  if (!checkInValue || !checkOutValue) return 0;
  const start = parseDateValue(checkInValue);
  const end = parseDateValue(checkOutValue);
  if (!start || !end) return 0;
  const nights = Math.ceil((end - start) / (1000 * 60 * 60 * 24));
  return Number.isFinite(nights) && nights > 0 ? nights : 0;
}

function formatBookingRate(details) {
  return `${localize(BOOKING_TEXT.baseRate)}: ${formatCurrency(details.price)} ${localize(details.unit)}`;
}

function setStayPresetMode(form, mode = "") {
  if (!form) return;
  form.dataset.stayPresetMode = mode;
}

function getStayPresetMode(form) {
  return form?.dataset.stayPresetMode || "";
}

// الإعدادات الجاهزة لمدة الإقامة توفر طريقة أسرع لاشتقاق تاريخ المغادرة من تاريخ الوصول.
function syncStayPlanner(form) {
  if (!form) return;
  const summaryNode = form.querySelector("[data-stay-plan-summary]");
  const buttons = Array.from(form.querySelectorAll("[data-stay-preset]"));
  if (!buttons.length) return;

  const checkIn = form.querySelector("[name='checkin']");
  const checkOut = form.querySelector("[name='checkout']");
  const checkInValue = checkIn?.value || "";
  const checkOutValue = checkOut?.value || "";
  const nights = calculateNights(checkInValue, checkOutValue);

  let mode = getStayPresetMode(form);
  if (!checkInValue) {
    mode = "";
  } else if (checkOutValue) {
    mode = [1, 2, 3].includes(nights) ? `${nights}` : "custom";
  } else if (mode !== "custom") {
    mode = "";
  }
  setStayPresetMode(form, mode);

  buttons.forEach((button) => {
    const preset = button.dataset.stayPreset || "";
    button.disabled = !checkInValue;
    button.classList.toggle("active", preset === mode);
  });

  if (!summaryNode) return;
  if (!checkInValue) {
    summaryNode.textContent = localize(BOOKING_TEXT.stayPlanPending);
    return;
  }
  if (!checkOutValue) {
    summaryNode.textContent = `${localize(BOOKING_TEXT.stayPlanAwaitingDeparture)} ${formatDisplayDate(checkInValue)}. ${localize(BOOKING_TEXT.stayPlanChooseDeparture)}`;
    return;
  }
  if (nights > 0) {
    summaryNode.textContent = `${formatDisplayDate(checkInValue)} - ${formatDisplayDate(checkOutValue)} | ${nights} ${localize(nights === 1 ? BOOKING_TEXT.nightSingular : BOOKING_TEXT.nightPlural)}`;
    return;
  }
  summaryNode.textContent = localize(BOOKING_TEXT.selectStayDates);
}

function applyStayPreset(form, nights) {
  const checkIn = form?.querySelector("[name='checkin']");
  const checkOut = form?.querySelector("[name='checkout']");
  if (!form || !checkIn || !checkOut) return;

  if (nights === "custom") {
    setStayPresetMode(form, "custom");
    syncStayPlanner(form);
    if (typeof checkOut.showPicker === "function") {
      try {
        checkOut.showPicker();
        return;
      } catch (_error) {
        // الرجوع إلى التركيز على الحقل عند عدم توفر واجهة اختيار التاريخ.
      }
    }
    checkOut.focus();
    return;
  }

  if (!checkIn.value) {
    if (typeof checkIn.showPicker === "function") {
      try {
        checkIn.showPicker();
        return;
      } catch (_error) {
        // الرجوع إلى التركيز على الحقل عند عدم توفر واجهة اختيار التاريخ.
      }
    }
    checkIn.focus();
    return;
  }

  const arrival = parseDateValue(checkIn.value);
  if (!arrival) return;
  arrival.setDate(arrival.getDate() + Number(nights));
  checkOut.value = formatDateValue(arrival);
  setStayPresetMode(form, `${nights}`);
  validateField(checkOut);
  updateBookingSummary(form);
}

function initStayPlanner(form) {
  const buttons = Array.from(form.querySelectorAll("[data-stay-preset]"));
  if (!buttons.length) return;

  const checkIn = form.querySelector("[name='checkin']");
  const checkOut = form.querySelector("[name='checkout']");
  if (!checkIn || !checkOut) return;

  buttons.forEach((button) => {
    button.addEventListener("click", () => {
      applyStayPreset(form, button.dataset.stayPreset || "");
    });
  });

  checkIn.addEventListener("change", () => {
    const mode = getStayPresetMode(form);
    if (mode && mode !== "custom") {
      applyStayPreset(form, mode);
      return;
    }
    syncStayPlanner(form);
  });

  checkOut.addEventListener("change", () => syncStayPlanner(form));
  syncStayPlanner(form);
}

// إبقاء بطاقة ملخص الحجز متزامنة مع تغييرات الباقة والتاريخ ومدة الإقامة.
function updateBookingSummary(form) {
  const packageKey = form.querySelector("[name='package']")?.value || "classic";
  const checkInValue = form.querySelector("[name='checkin']")?.value || "";
  const checkOutValue = form.querySelector("[name='checkout']")?.value || "";
  const details = getPackageDetails(packageKey);
  const packageLabel = localize(details.label);
  const packageNote = localize(details.note);
  const packagePerks = localize(details.perks);
  const nights = calculateNights(checkInValue, checkOutValue);
  const multiplier = packageKey === "daycare" ? Math.max(nights, checkInValue ? 1 : 0) : Math.max(nights, 1);
  const total = details.price * multiplier;

  const packageNode = document.querySelector("[data-booking-package]");
  const noteNode = document.querySelector("[data-booking-note]");
  const arrivalNode = document.querySelector("[data-booking-arrival]");
  const durationNode = document.querySelector("[data-booking-duration]");
  const priceNode = document.querySelector("[data-booking-price]");
  const priceMetaNode = document.querySelector("[data-booking-price-meta]");
  const perksNode = document.querySelector("[data-booking-perks]");

  if (packageNode) packageNode.textContent = packageLabel;
  if (noteNode) noteNode.textContent = packageNote;
  if (arrivalNode) arrivalNode.textContent = checkInValue ? formatDisplayDate(checkInValue) : localize(BOOKING_TEXT.chooseArrival);
  if (durationNode) {
    durationNode.textContent =
      packageKey === "daycare"
        ? checkInValue
          ? localize(BOOKING_TEXT.singleDayVisit)
          : localize(BOOKING_TEXT.pickDateFirst)
        : nights > 0
          ? `${nights} ${localize(nights === 1 ? BOOKING_TEXT.nightSingular : BOOKING_TEXT.nightPlural)}`
          : localize(BOOKING_TEXT.selectStayDates);
  }
  if (priceNode) {
    priceNode.textContent =
      checkInValue || packageKey === "classic" || packageKey === "premium"
        ? formatCurrency(total)
        : formatCurrency(details.price);
  }
  if (priceMetaNode) {
    priceMetaNode.textContent = formatBookingRate(details);
  }
  if (perksNode) {
    perksNode.innerHTML = packagePerks.map((item) => `<li>${item}</li>`).join("");
  }

  document.querySelectorAll("[data-package-select]").forEach((button) => {
    button.classList.toggle("active", button.dataset.packageSelect === packageKey);
  });

  syncStayPlanner(form);
}

function initPackageSelector() {
  const form = document.querySelector("[data-booking-form]");
  if (!form) return;
  const packageField = form.querySelector("[name='package']");
  if (!packageField) return;

  document.querySelectorAll("[data-package-select]").forEach((button) => {
    button.addEventListener("click", () => {
      packageField.value = button.dataset.packageSelect || "classic";
      validateField(packageField);
      updateBookingSummary(form);
    });
  });

  const params = new URLSearchParams(window.location.search);
  const packageFromQuery = params.get("package");
  if (packageFromQuery && PACKAGE_DETAILS[packageFromQuery]) {
    packageField.value = packageFromQuery;
  }

  packageField.addEventListener("change", () => updateBookingSummary(form));
  updateBookingSummary(form);
}

// المعالجة المشتركة للنماذج تغطي نماذج الحجز والتواصل دون تغيير بنية الصفحة.
function initForms() {
  document.querySelectorAll("form").forEach((form) => {
    const successPanel =
      form.parentElement?.querySelector("[data-form-success]") ||
      form.closest(".contact-message-panel, .booking-shell, .section-frame")?.querySelector("[data-form-success]");
    const fields = form.querySelectorAll("input, select, textarea");

    initDateInputShells(form);
    initStayPlanner(form);
    setMinimumDates(form);

    fields.forEach((field) => {
      field.addEventListener("blur", () => validateField(field));
      field.addEventListener("input", () => {
        successPanel?.classList.remove("active");
        if (field.classList.contains("field-error")) validateField(field);
      });
    });

    form.addEventListener("submit", (event) => {
      event.preventDefault();
      let valid = true;

      fields.forEach((field) => {
        if (!validateField(field)) valid = false;
      });

      const checkIn = form.querySelector("[name='checkin']");
      const checkOut = form.querySelector("[name='checkout']");
      if (checkIn && checkOut && checkIn.value && checkOut.value) {
        const checkInDate = parseDateValue(checkIn.value);
        const checkOutDate = parseDateValue(checkOut.value);
        if (checkInDate && checkOutDate && checkOutDate <= checkInDate) {
          valid = false;
          const visualTarget = getFieldVisualTarget(checkOut);
          visualTarget?.classList.add("field-error");
          checkOut.classList.remove("field-error");
          const errorNode = getFieldErrorNode(checkOut);
          if (errorNode) errorNode.textContent = localize(FORM_ERRORS.checkout);
        }
      }

      if (!valid) return;

      form.reset();
      if (checkIn && checkOut) {
        const today = new Date().toISOString().split("T")[0];
        checkIn.min = today;
        checkOut.min = today;
      }
      setStayPresetMode(form, "");
      successPanel?.classList.add("active");
      successPanel?.scrollIntoView({ behavior: "smooth", block: "nearest" });
      if (form.matches("[data-booking-form]")) {
        updateBookingSummary(form);
      }
    });
  });
}

// عناصر الأسئلة الشائعة تعمل كأكورديون خفيف مع مزامنة حالة إمكانية الوصول.
function initFaq() {
  document.querySelectorAll("[data-faq-item]").forEach((item) => {
    const trigger = item.querySelector("[data-faq-trigger]");
    const sync = () => trigger?.setAttribute("aria-expanded", item.classList.contains("open") ? "true" : "false");
    sync();
    trigger?.addEventListener("click", () => {
      item.classList.toggle("open");
      sync();
    });
  });
}

// تأثير الميلان المعتمد على المؤشر يُفعّل فقط على الأجهزة القادرة على عرضه بسلاسة.
function initTilt() {
  if (window.matchMedia("(prefers-reduced-motion: reduce)").matches) return;
  if (!window.matchMedia("(hover: hover) and (pointer: fine)").matches) return;

  document.querySelectorAll(".tilt-card").forEach((card) => {
    let rect = null;
    let rafId = 0;
    let nextX = 0;
    let nextY = 0;

    const paint = () => {
      rafId = 0;
      card.style.transform = `rotateX(${(-nextY * 8).toFixed(2)}deg) rotateY(${(nextX * 10).toFixed(2)}deg) translateY(-4px)`;
    };

    card.addEventListener("pointerenter", () => {
      rect = card.getBoundingClientRect();
    });

    card.addEventListener("pointermove", (event) => {
      const bounds = rect || card.getBoundingClientRect();
      nextX = (event.clientX - bounds.left) / bounds.width - 0.5;
      nextY = (event.clientY - bounds.top) / bounds.height - 0.5;

      if (rafId) return;
      rafId = window.requestAnimationFrame(paint);
    });

    card.addEventListener("pointerleave", () => {
      rect = null;
      if (rafId) {
        window.cancelAnimationFrame(rafId);
        rafId = 0;
      }
      card.style.transform = "";
    });
  });
}

// نافذة المعرض تضبط الإطار حسب اتجاه الصورة واللغة النشطة.
function initLightbox() {
  if (!appState.lightbox) return;

  const panel = appState.lightbox.querySelector("[data-lightbox-panel]");
  const image = appState.lightbox.querySelector("[data-lightbox-image]") || appState.lightbox.querySelector("img");
  const caption = appState.lightbox.querySelector("[data-lightbox-caption]");
  const closeButton = appState.lightbox.querySelector("[data-lightbox-close]");
  const resolveOrientation = (width, height, fallbackCard) => {
    if (width > 0 && height > 0) {
      const ratio = width / height;
      if (ratio < 0.86) return "portrait";
      if (ratio > 1.14) return "landscape";
      return "square";
    }
    if (fallbackCard?.classList.contains("gallery-curation-card--portrait")) return "portrait";
    return "landscape";
  };

  const syncLightboxFrame = (card, width = 0, height = 0) => {
    if (!panel) return;
    panel.dataset.lightboxOrientation = resolveOrientation(width, height, card);
  };

  document.querySelectorAll("[data-lightbox]").forEach((card) => {
    card.addEventListener("click", () => {
      const previewImage = card.querySelector("img");
      const nextCaption =
        getCurrentLanguage() === "ar"
          ? card.dataset.captionAr || card.dataset.caption || ""
          : card.dataset.caption || card.dataset.captionAr || "";
      const nextAlt =
        getCurrentLanguage() === "ar"
          ? previewImage?.dataset.arAlt || card.dataset.imageAlt || previewImage?.alt || ""
          : previewImage?.dataset.enAlt || card.dataset.imageAlt || previewImage?.alt || "";

      syncLightboxFrame(card, previewImage?.naturalWidth || 0, previewImage?.naturalHeight || 0);
      image.onload = () => syncLightboxFrame(card, image.naturalWidth, image.naturalHeight);
      image.src = card.dataset.imageSrc || "";
      image.alt = nextAlt;
      image.decoding = "async";
      caption.textContent = nextCaption;
      appState.lightbox.classList.add("open");
      lockScroll("lightbox");
    });
  });

  const closeLightbox = () => {
    appState.lightbox.classList.remove("open");
    if (panel) panel.dataset.lightboxOrientation = "landscape";
    if (image) image.onload = null;
    unlockScroll("lightbox");
  };

  closeButton?.addEventListener("click", closeLightbox);
  appState.lightbox.addEventListener("click", (event) => {
    if (event.target === appState.lightbox) closeLightbox();
  });
}

// منطق السلايدر يدعم التحكم اليدوي والتشغيل التلقائي والإيقاف المؤقت بحسب الظهور.
function initSlider() {
  document.querySelectorAll("[data-slider]").forEach((slider) => {
    if (slider.dataset.sliderReady === "true") return;
    slider.dataset.sliderReady = "true";

    const track = slider.querySelector("[data-slider-track]");
    const slides = slider.querySelectorAll("[data-slide]");
    const next = slider.querySelector("[data-slider-next]");
    const prev = slider.querySelector("[data-slider-prev]");
    const dotsHost = slider.querySelector("[data-slider-dots]");
    if (!track || !slides.length) return;

    let index = 0;
    let intervalId = 0;
    let isVisible = false;
    let isHovered = false;
    let isFocused = false;

    const dots = dotsHost
      ? Array.from(slides, (_, slideIndex) => {
          const dot = document.createElement("button");
          dot.type = "button";
          dot.className = "testimonial-dot";
          dot.setAttribute("aria-label", sliderDotLabel(slideIndex));
          dot.addEventListener("click", () => {
            index = slideIndex;
            paint();
            syncAuto();
          });
          dotsHost.appendChild(dot);
          return dot;
        })
      : [];

    const paint = () => {
      slider.dataset.index = String(index);
      track.style.transform = `translate3d(${-index * 100}%, 0, 0)`;
      dots.forEach((dot, dotIndex) => {
        const active = dotIndex === index;
        dot.classList.toggle("active", active);
        dot.setAttribute("aria-current", active ? "true" : "false");
      });
    };

    const goNext = () => {
      index = (index + 1) % slides.length;
      paint();
    };

    const goPrev = () => {
      index = (index - 1 + slides.length) % slides.length;
      paint();
    };

    next?.addEventListener("click", goNext);
    prev?.addEventListener("click", goPrev);

    const stopAuto = () => {
      if (intervalId) window.clearInterval(intervalId);
      intervalId = 0;
    };

    const syncAuto = () => {
      if (
        window.matchMedia("(prefers-reduced-motion: reduce)").matches ||
        slides.length < 2 ||
        !isVisible ||
        isHovered ||
        isFocused ||
        document.hidden
      ) {
        stopAuto();
        return;
      }

      if (intervalId) return;
      intervalId = window.setInterval(goNext, 5400);
    };

    slider.addEventListener("mouseenter", () => {
      isHovered = true;
      stopAuto();
    });

    slider.addEventListener("mouseleave", () => {
      isHovered = false;
      syncAuto();
    });

    slider.addEventListener("focusin", () => {
      isFocused = true;
      stopAuto();
    });

    slider.addEventListener("focusout", (event) => {
      if (event.relatedTarget instanceof Node && slider.contains(event.relatedTarget)) return;
      isFocused = false;
      syncAuto();
    });

    document.addEventListener("visibilitychange", syncAuto);

    if ("IntersectionObserver" in window) {
      const observer = new IntersectionObserver(
        (entries) => {
          entries.forEach((entry) => {
            isVisible = entry.isIntersecting;
            syncAuto();
          });
        },
        { threshold: 0.4 }
      );
      observer.observe(slider);
    } else {
      isVisible = true;
    }

    paint();
    syncAuto();
  });
}

// حركة دخول خفيفة تعتمد على CSS و IntersectionObserver لتقليل العمل على الخيط الرئيسي.
function initAnimations() {
  markRevealTargets();
  initHeroParallax();
  initIntersectionReveals();
}

// الأحداث المفوضة عالميًا تدير الإعدادات واللغة والثيم وتنقل الجوال.
function bindGlobalUiEvents() {
  if (appState.isInitialized) return;

  document.addEventListener("click", (event) => {
    const target = getEventElement(event);
    if (!target) return;

    if (
      appState.settingsOpen &&
      !target.closest("[data-accessibility-panel]") &&
      !target.closest("[data-settings-toggle]")
    ) {
      closeSettings();
    }

    if (target.closest("[data-settings-toggle]")) {
      toggleSettings();
      return;
    }

    if (target.closest("[data-menu-toggle]")) {
      toggleMenu();
      return;
    }

    if (target.closest("[data-menu-close]")) {
      closeMenu();
      return;
    }

    if (target.closest("[data-mobile-overlay]")) {
      closeMenu();
      return;
    }

    if (target.closest("[data-mobile-drawer] a")) {
      closeMenu();
      return;
    }

    if (target.closest("[data-theme-toggle]")) {
      applyTheme(body.classList.contains("dark-mode") ? "light" : "dark");
      return;
    }

    if (target.closest("[data-language-toggle]")) {
      applyLanguage(getCurrentLanguage() === "ar" ? "en" : "ar");
      return;
    }

    if (target.closest("[data-font='increase']")) {
      const current = parseFloat(getComputedStyle(root).getPropertyValue("--font-scale")) || 1;
      applyFontScale(current + 0.05);
      return;
    }

    if (target.closest("[data-font='decrease']")) {
      const current = parseFloat(getComputedStyle(root).getPropertyValue("--font-scale")) || 1;
      applyFontScale(current - 0.05);
    }
  });

  appState.isInitialized = true;
}

// مستمعو دورة الحياة يعالجون تغيّر الحجم واستعادة bfcache وتنظيف الحالات عند الضغط على Escape.
function bindLifecycleEvents() {
  if (appState.hasLifecycleListeners) return;

  // قد تعيد متصفحات الجوال الصفحة من bfcache مع بقاء حالة قائمة قديمة مفتوحة.
  window.addEventListener("pageshow", () => {
    resetMenuState();
    closeSettings();
    initActiveSectionNav();
    syncScrollState();
  });

  window.addEventListener("resize", () => {
    if (appState.resizeTimer) window.clearTimeout(appState.resizeTimer);
    appState.resizeTimer = window.setTimeout(() => {
      if (window.innerWidth >= 1280) closeMenu();
      closeSettings();
      if (appState.activeScrollLocks.size > 0) {
        setScrollbarCompensation(window.innerWidth - document.documentElement.clientWidth);
      }
      initActiveSectionNav();
      syncScrollState();
    }, 120);
  });

  window.addEventListener("keydown", (event) => {
    if (event.key !== "Escape") return;
    closeSettings();
    closeMenu();
    if (appState.lightbox?.classList.contains("open")) {
      appState.lightbox.classList.remove("open");
      unlockScroll("lightbox");
    }
  });

  appState.hasLifecycleListeners = true;
}

function requestScrollSync() {
  if (appState.scrollRafId) return;
  appState.scrollRafId = window.requestAnimationFrame(() => {
    appState.scrollRafId = 0;
    syncScrollState();
  });
}

function initMenu() {
  resetMenuState();
  syncScrollState();
  if (!appState.hasScrollListener) {
    window.addEventListener("scroll", requestScrollSync, { passive: true });
    appState.hasScrollListener = true;
  }
}

// تهيئة الهيكل المشترك أولًا ثم تحسين الوسائط والنماذج والحركات تدريجيًا.
function init() {
  ensureRuntimeStyles();
  const savedLanguage = readStoredPreference(STORAGE_KEYS.language, DEFAULT_LANGUAGE);
  syncDocumentLanguage(savedLanguage);
  renderShell();
  applyLocalizedContent();
  cacheUi();
  initLoader();
  optimizeImages();
  initActiveSectionNav();
  bindGlobalUiEvents();
  bindLifecycleEvents();

  const savedTheme = readStoredPreference(STORAGE_KEYS.theme, "dark");
  const savedScale = parseFloat(readStoredPreference(STORAGE_KEYS.fontScale, "1") || "1");

  applyTheme(savedTheme);
  applyFontScale(savedScale);
  // Keep theme strictly driven by global state.
  // Ensure body is visible after theme/class application.
  document.body.style.visibility = "visible";
  document.body.style.opacity = "1";
  setCurrentYear();

  initMenu();
  initScrollTop();
  initFaq();
  initLightbox();
  initSlider();
  initPackageSelector();
  initForms();
  window.requestAnimationFrame(() => {
    initStatCounters();
    initAnimations();
    if ("requestIdleCallback" in window) {
      window.requestIdleCallback(
        () => {
          initTilt();
          initHeroVideoPlayback();
        },
        { timeout: 1200 }
      );
      return;
    }

    window.setTimeout(() => {
      initTilt();
      initHeroVideoPlayback();
    }, 120);
  });
}

if (document.readyState === "loading") {
  document.addEventListener("DOMContentLoaded", init);
} else {
  init();
}
