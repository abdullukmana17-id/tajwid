# Install library
```bash
npm install autosize
```
```bash
npm install react-router-dom
```

# Menentukan Struktur Folder

```bash
src/
├── assets/
│   ├── images/
│   ├── fonts/
├── components/
│   ├── Textarea.jsx
├── layouts/
│   ├── Brand.jsx
│   ├── Footer.jsx
│   ├── Header.jsx
│   ├── Nav.jsx
│   ├── Sidebar.jsx
│   ├── index.jsx
├── pages/
│   ├── Home.jsx
│   ├── Surah.jsx
│   ├── Ayah.jsx
│   ├── Editor.jsx
├── router/
├── services/
│   ├── api.js
│   ├── ayah.service.js
├── hooks/
│   ├── Tajwid.jsx
├── utils/
│   ├── Surah.jsx
├── styles/
│   ├── index.css
│   ├── header.css
│   ├── footer.css
│   ├── nav.css
│   ├── sidebar.css
│   ├── surah.css
│   ├── ayah.css
│   ├── home.css
│   ├── editor.css
│   ├── variabel.css
├── App.jsx
└── main.jsx
```

## Textarea.jsx
```jsx
import { useEffect, useRef } from "react";
import autosize from "autosize";

export default function Textarea({ 
    id, 
    label, 
    placeholder = "",
    value,
    defaultValue = "",
    onChange,
    className,
    ...props 
}) {
  const taRef = useRef(null);

  useEffect(() => {
    autosize(taRef.current);
    return () => autosize.destroy(taRef.current);
  }, []);

  useEffect(() => {
    if (taRef.current) {
      autosize.update(taRef.current);
    }
  }, [value]);

  const isControlled = value !== undefined;

  return (
    <div className={`form-floating ${className || 'mb-3'}`}>
      <textarea
        ref={taRef}
        className="form-control rounded-4 px-3 font-lpmq fs-3 border-0 shadow-none"
        placeholder={placeholder}
        id={id}
        {...(isControlled
          ? { value, onChange }
          : { defaultValue, onChange }
        )}
        {...props}
      />
      <label className="ms-1" htmlFor={id}>{label}</label>
    </div>
  );
}
```

## Brand.jsx
```jsx
import { useContext } from "react"

export default function Brand() {
    return(
        <a href="#" className="navbar-brand link-dark text-decoration-none fs-5 font-serif text-primary">
            Al-Quran
        </a>
    )
}
```

## Footer.jsx
```jsx
export default function Footer() {
    return(
        <></>
    )
}
```

## Header.jsx
```jsx
import { forwardRef } from "react";
import { PrimaryOffcanvasButton } from ".";

const Header = forwardRef(({ title, children, className, navs, ...props }, ref) => {
    return (
        <header
            ref={ref}
            className={`bg-body position-sticky top-0 ${className || 'py-2'} z-3`}
            {...props}
        >
            <div className="container-fluid">
                <div className="row justify-content-center mt-1">
                    <div className="col-12 d-flex align-items-center gap-2">
                        <PrimaryOffcanvasButton className="d-sm-none btn-light mt-1" />

                        {title && (
                            <>
                                <h1 className="mb-0 font-sans fw-normal fs-6 me-auto">
                                    <small>{title}</small>
                                </h1>
                                {navs}
                            </>
                        )}
                    </div>
                </div>
            </div>

            {children}
        </header>
    );
});

Header.displayName = "Header";

export default Header;
```

## Nav.jsx
```jsx
import { useId, useEffect, useRef, useState } from "react";
import { NavLink, useLocation } from "react-router-dom";
import { useSidebarLayout } from ".";

function hasCategory(obj) {
    return obj.hasOwnProperty("category");
}

function useIsWide(breakpoint = 576) {
    const [isWide, setIsWide] = useState(() => window.innerWidth > breakpoint);

    useEffect(() => {
        const handler = () => setIsWide(window.innerWidth > breakpoint);
        window.addEventListener("resize", handler);
        return () => window.removeEventListener("resize", handler);
    }, [breakpoint]);

    return isWide;
}

export default function Nav({menus}) {
    const { primaryHidden } = useSidebarLayout();
    const isWide = useIsWide(576);
    const navRef = useRef(null);

    const showTooltips = primaryHidden && isWide;

    useEffect(() => {
        if (!window.bootstrap || !navRef.current) return;
        const els = navRef.current.querySelectorAll(".has-tooltip");
        const instances = Array.from(els).map((el) =>
            window.bootstrap.Tooltip.getOrCreateInstance(el, {
                html: false,
                placement: el.dataset.bsPlacement || "right",
            })
        );
        return () => instances.forEach((i) => i.dispose());
    });

    return (
        <div ref={navRef}>
            {menus && (
                <Accordion>
                    <AccordionItem menus={menus} showTooltips={showTooltips} />
                </Accordion>
            )}
        </div>
    );
}

function Accordion({ children }) {
    const accordionId = useId();

    return (
        <div className="accordion" id={`accordion-${accordionId}`}>
            {children}
        </div>
    );
}

function AccordionItem({ menus, depth = 0, showTooltips }) {
    return (
        <>
            {menus.map((menu, index) => {
                if (hasCategory(menu)) {
                    return (
                        <AccordionCategory
                            depth={depth}
                            key={index}
                            menu={menu}
                            showTooltips={showTooltips}
                        />
                    );
                }

                const baseClassName = [
                    "text-decoration-none btn rounded-2 mb-1 w-100 text-start p-2 py-0 d-flex align-items-center",
                    showTooltips ? "has-tooltip" : "",
                ]
                    .filter(Boolean)
                    .join(" ");

                const commonProps = {
                    style: { "--depth": depth },
                    "data-bs-title": showTooltips ? menu.title : undefined,
                    "data-bs-placement": "right",
                };

                return (
                    <div key={index} className="accordion-item p-0">
                        {menu.to ? (
                            <NavLink
                                {...commonProps}
                                to={menu.to}
                                end
                                className={({ isActive }) =>
                                    [baseClassName, isActive ? "btn-primary active text-nowrap" : "btn-light text-nowrap"]
                                        .join(" ")
                                }
                            >
                                {menu.label}
                            </NavLink>
                        ) : (
                            <a {...commonProps} className={`${baseClassName} btn-light text-nowrap`} href={menu.href}>
                                {menu.label}
                            </a>
                        )}
                    </div>
                );
            })}
        </>
    );
}

function hasActiveChild(menu, pathname) {
    if (!hasCategory(menu)) {
        return menu.to && pathname === menu.to;
    }
    return menu.menus?.some((child) => hasActiveChild(child, pathname));
}

function AccordionCategory({ menu, depth, showTooltips }) {
    const collapseId = useId();
    const headerId = useId();
    const safeCollapseId = `collapse-${collapseId.replace(/:/g, "")}`;
    const safeHeaderId = `header-${headerId.replace(/:/g, "")}`;

    const { pathname } = useLocation();
    const isOpen = hasActiveChild(menu, pathname);

    return (
        <div className="accordion-item">
            <h2 className="accordion-header" id={safeHeaderId}>
                <button
                    className={[
                        "accordion-button border btn btn-light p-2 py-0 rounded-2 mb-1 font-sans",
                        isOpen ? "" : "collapsed",
                        showTooltips ? "has-tooltip" : "",
                    ]
                        .filter(Boolean)
                        .join(" ")}
                    type="button"
                    style={{ "--depth": depth }}
                    data-bs-toggle="collapse"
                    data-bs-target={`#${safeCollapseId}`}
                    aria-expanded={isOpen ? "true" : "false"}
                    aria-controls={safeCollapseId}
                    data-bs-title={showTooltips ? menu.title : undefined}
                    data-bs-placement="right"
                >
                    {menu.category}
                </button>
            </h2>
            <div
                id={safeCollapseId}
                className={["accordion-collapse collapse", isOpen ? "show" : ""].join(" ")}
                aria-labelledby={safeHeaderId}
            >
                <div className="accordion-body p-0">
                    <Accordion>
                        <AccordionItem
                            depth={depth + 1}
                            menus={menu.menus}
                            showTooltips={showTooltips}
                        />
                    </Accordion>
                </div>
            </div>
        </div>
    );
}
```

## Sidebar.jsx
```jsx
import Brand from "./Brand"
import Nav from "./Nav";

const MENUS = [
    {
        to: "/",
        title: "Beranda",
        label: (
            <>
                <i className="bi bi-house"></i>
                <span className="ms-2">Beranda</span>
            </>
        ),
    },
    {
        to: "/quran",
        title: "Al - Qur`an",
        label: (
            <>
                <i className="bi bi-inboxes"></i>
                <span className="ms-2">Al - Qur`an</span>
            </>
        ),
    },
    {
        to: "/tajwid",
        title: "Highlight Tajwid",
        label: (
            <>
                <i className="bi bi-palette"></i>
                <span className="ms-2">Highlight Tajwid</span>
            </>
        ),
    },
]
    .map((menu) => {
        if (menu.menus) {
            return menu.menus.length > 0 ? menu : null;
        }
        return menu;
    })
    .filter(Boolean);

export default function Sidebar() {

    return (
        <>
            <div className="w-100 h-100 d-flex flex-column pt-2">
                <div className="container h-100">
                    <div className="row h-100 flex-nowrap flex-column align-items-start g-2">
                        <div className="col-12 px-0 pt-1 d-flex align-items-center justify-content-start mb-3 nav-menu">
                            <Brand />
                        </div>
                        <div className="col-12 px-0 nav-menu">
                            <Nav menus={menus} />
                        </div>
                    </div>
                </div>
            </div>
        </>
    )
}
```

## Home.jsx
```jsx
import Layout from "../layout";
import bg from "../image/bg.jpg";
import SURAHS from "../const/Surah";
import Sidebar from "../layout/Sidebar";
import { PrimaryToggleButton, PrimaryOffcanvasButton } from "../layout";
import Brand from "../layout/Brand";
import { Link } from "react-router-dom";

export default function Home() {
    const numbers = [1, 2, 3, 4, 5, 6];
    const surahPopuler = SURAHS.filter(surah => numbers.includes(surah.number));
    const dummyAyat = {
        1: "بِسْمِ ٱللَّهِ ٱلرَّحْمَـٰنِ ٱلرَّحِيمِ",
        2: "أُو۟لَـٰٓئِكَ عَلَىٰ هُدًۭى مِّن رَّبِّهِمْ",
        3: "وَإِنَّ ٱللَّهَ لَعَلِيمٌ حَكِيمٌ",
        4: "إِنَّ ٱلَّذِينَ كَفَرُوا۟",
        5: "فَسَوْفَ يَعْلَمُونَ",
        6: "وَمَا يَشْعُرُونَ"
    };

    const terakhirDibaca = SURAHS
        .filter(surah => numbers.includes(surah.number))
        .map(surah => ({
            ...surah,
            ayatTerakhir: 1,
            ayatArab: dummyAyat[surah.number]
        }));

    return (
        <Layout
            primaryMinWidth="calc(2.25rem + 1rem)"
            primarySidebarContent={
                <Sidebar />
            }
            mainContent={
                <>
                    <header className="position-sticky top-0 p-2 px-lg-4 z-3 bg-body">
                        <div className="container">
                            <div className="row">
                                <div className="col-12 d-flex gap-2 align-items-center">
                                    <PrimaryOffcanvasButton className="btn btn-light d-sm-none" />
                                    <div className="d-lg-none">
                                        <Brand />
                                    </div>
                                    <PrimaryToggleButton className="btn-light ms-auto d-none d-sm-flex">
                                        <i className="bi bi-list"></i>
                                    </PrimaryToggleButton>
                                </div>
                            </div>
                        </div>
                    </header>
                    <section className="p-2 px-lg-4 banner font-sans">
                        <div className="container">
                            <div className="row justify-content-center">
                                <div className="col-12">
                                    <div className="card border position-relative overflow-hidden rounded-4 z-1">
                                        <img src={bg} className="w-100 object-fit-cover position-absolute top-0 start-0 h-100" alt="" />
                                        <div className="card-body z-1 p-3 p-md-5">
                                            <div className="row">
                                                <div className="col-10 col-sm-9 col-md-8 col-lg-7 text-white">
                                                    <h6 className="display-6 font-serif">Bacalah Al-Quran dan Tenangkan Hatimu</h6>
                                                    <p className="m-0">Sesungguhnya dengan mengingat Allah, hati menjadi tenang</p>
                                                    <p>(QS. Ar-Ra'd:28)</p>
                                                    <Link
                                                        to="/quran"
                                                        className="btn btn-custom-primary me-2 d-none d-md-inline"
                                                    >
                                                        <i className="bi bi-book me-2"></i>
                                                        Baca Al-Quran
                                                    </Link>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div className="col-12 d-md-none mt-2 d-flex align-items-center justify-content-center">
                                    <button type="button" className="btn btn-custom-primary rounded-3 py-2 me-2 w-50 text-nowrap">
                                        <i className="bi bi-book me-2"></i>
                                        Baca Al-Quran
                                    </button>
                                </div>
                            </div>
                        </div>
                    </section>
                    <section className="p-2 p-lg-4 mt-3 font-sans">
                        <div className="container">
                            <div className="row">
                                <div className="col-12 d-flex justify-content-between align-items-center">
                                    <h6 className="mb-0 font-serif fs-4">Surah Populer</h6>
                                    <p className="m-0 text-success">Lihat semua</p>
                                </div>
                            </div>
                            <div className="row mt-3 g-2">
                                {surahPopuler.map((surah) => (
                                    <div className="col-auto flex-grow-1" key={surah.number}>
                                        <Link
                                            to={`/surahs/${surah.number}`}
                                            className="text-decoration-none text-reset"
                                        >
                                            <div className="card rounded-4 border h-100">
                                                <div className="card-body pt-0">
                                                    <div className="icon flex-shrink-0 me-3">
                                                        {surah.number}
                                                    </div>

                                                    <h6 className="font-thuluth display-4 d-inline-block mb-0">
                                                        {surah.arabic}
                                                    </h6>

                                                    <p className="mb-0 fw-bold">
                                                        {surah.name}
                                                    </p>

                                                    <p className="mb-0">
                                                        {surah.ayahCount} Ayat
                                                    </p>
                                                </div>
                                            </div>
                                        </Link>
                                    </div>
                                ))}
                            </div>
                        </div>
                    </section>
                </>
            }
        />
    );
}
```

## Surah.jsx
```jsx
import Layout from "../layout";
import Sidebar from "../layout/Sidebar";
import { PrimaryToggleButton, PrimaryOffcanvasButton } from "../layout";
import Brand from "../layout/Brand";
import { useState } from "react";
import SURAHS from "../utils/Surah";
import { Link } from "react-router-dom";

export default function SurahList() {
    const [searchQuery, setSearchQuery] = useState("");

    const filteredSurahs = SURAHS.filter(surah =>
        surah.name.toLowerCase().includes(searchQuery.toLowerCase()) ||
        surah.translation.toLowerCase().includes(searchQuery.toLowerCase()) ||
        surah.number.toString().includes(searchQuery)
    );

    return (
        <Layout
            primaryMinWidth="calc(2.25rem + 1rem)"
            primarySidebarContent={
                <Sidebar />
            }
            mainContent={
                <>
                    <style>{`
                        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&family=Amiri:wght@400;700&display=swap');

                        :root {
                            --primary-color: #0f3460;
                            --accent-color: #d4af37;
                            --light-color: #f8f9fa;
                            --border-color: #e8eef7;
                            --text-dark: #1a2332;
                            --text-muted: #6b7280;
                        }

                        @media (prefers-color-scheme: dark) {
                            :root {
                                --primary-color: #1a5f7a;
                                --accent-color: #ffd700;
                                --light-color: #1f2937;
                                --border-color: #374151;
                                --text-dark: #f3f4f6;
                                --text-muted: #9ca3af;
                            }
                        }

                        @keyframes slideUp {
                            from {
                                opacity: 0;
                                transform: translateY(8px);
                            }
                            to {
                                opacity: 1;
                                transform: translateY(0);
                            }
                        }

                        .surah-card {
                            border-radius: 24px;
                            border: 1px solid var(--border-color);
                            animation: slideUp 0.6s ease-out;
                            transition: all 0.3s ease;
                            background: var(--light-color);
                        }

                        .surah-card:hover {
                            transform: translateY(-4px);
                            border-color: var(--primary-color);
                            box-shadow: 0 8px 32px rgba(15, 52, 96, 0.12);
                        }

                        .surah-card-body {
                            padding: 28px 24px;
                            display: flex;
                            align-items: flex-start;
                            gap: 16px;
                        }

                        .surah-card-icon {
                            width: 48px;
                            height: 48px;
                            border-radius: 50%;
                            background: linear-gradient(135deg, var(--primary-color), #0a2340);
                            display: flex;
                            align-items: center;
                            justify-content: center;
                            flex-shrink: 0;
                            color: white;
                            font-weight: 600;
                            font-size: 20px;
                            box-shadow: 0 4px 12px rgba(15, 52, 96, 0.15);
                            transition: transform 0.3s ease;
                        }

                        .surah-card:hover .surah-card-icon {
                            transform: scale(1.08);
                        }

                        .surah-card-content {
                            flex: 1;
                            min-width: 0;
                        }

                        .surah-arabic-name {
                            font-size: 32px;
                            font-weight: 700;
                            color: var(--primary-color);
                            margin: 0;
                            margin-bottom: 8px;
                            line-height: 1.2;
                            word-break: break-word;
                        }

                        .surah-name {
                            font-size: 16px;
                            font-weight: 600;
                            color: var(--text-dark);
                            margin: 0;
                            margin-bottom: 6px;
                        }

                        .surah-ayah-count {
                            font-size: 14px;
                            color: var(--text-muted);
                            margin: 0;
                            font-weight: 400;
                        }

                        .search-input-wrapper {
                            position: relative;
                        }

                        .search-input-wrapper .input-group-text {
                            background: var(--light-color);
                            border: 1px solid var(--border-color);
                            color: var(--primary-color);
                        }

                        .search-input-wrapper .form-control {
                            border: 1px solid var(--border-color);
                        }

                        .search-input-wrapper .form-control:focus {
                            border-color: var(--primary-color);
                            box-shadow: 0 0 0 0.2rem rgba(15, 52, 96, 0.25);
                        }

                        h1.surah-list-title {
                            color: var(--text-dark);
                        }

                        .surah-list-subtitle {
                            color: var(--text-muted);
                        }

                        .surah-summary {
                            color: var(--text-muted);
                        }
                    `}</style>

                    <header className="position-sticky top-0 p-2 px-lg-4 z-3 bg-body border-bottom">
                        <div className="container">
                            <div className="row">
                                <div className="col-12 d-flex gap-2 align-items-center">
                                    <PrimaryOffcanvasButton className="btn btn-light d-sm-none" />
                                    <div className="d-lg-none">
                                        <Brand />
                                    </div>
                                    <PrimaryToggleButton className="btn-light ms-auto d-none d-sm-flex">
                                        <i className="bi bi-list"></i>
                                    </PrimaryToggleButton>
                                </div>
                            </div>
                        </div>
                    </header>

                    <section className="py-4">
                        <div className="container">
                            {/* Header Section */}
                            <div className="row mb-4">
                                <div className="col-12">
                                    <h1 className="h3 fw-bold mb-2 surah-list-title">Daftar Surah</h1>
                                    <p className="surah-list-subtitle">Pilih surah untuk membaca dan mempelajari tajwid</p>
                                </div>
                            </div>

                            {/* Search Section */}
                            <div className="row mb-4">
                                <div className="col-12 col-md-6">
                                    <div className="input-group search-input-wrapper">
                                        <span className="input-group-text">
                                            <i className="bi bi-search"></i>
                                        </span>
                                        <input
                                            type="text"
                                            className="form-control"
                                            placeholder="Cari surah..."
                                            value={searchQuery}
                                            onChange={(e) => setSearchQuery(e.target.value)}
                                        />
                                    </div>
                                </div>
                            </div>

                            {/* Surahs List */}
                            <div className="row g-3">
                                {filteredSurahs.length > 0 ? (
                                    filteredSurahs.map((surah) => (
                                        <div key={surah.number} className="col-12 col-sm-6 col-lg-4">
                                            <Link
                                                to={`/surahs/${surah.number}`}
                                                className="text-decoration-none"
                                            >
                                                <div className="card surah-card rounded-4 border h-100">
                                                    <div className="card-body pt-0 d-flex align-items-center">
                                                        <div className="surah-card-icon me-3">
                                                            {surah.number}
                                                        </div>

                                                        <div className="detail">
                                                            <h6
                                                                className="font-thuluth fs-3"
                                                                dir="rtl"
                                                            >
                                                                {surah.arabic}
                                                            </h6>

                                                            <p className="surah-name">
                                                                {surah.name}
                                                            </p>

                                                            <p className="surah-ayah-count">
                                                                {surah.ayahCount} Ayat
                                                            </p>
                                                        </div>
                                                    </div>
                                                </div>
                                            </Link>
                                        </div>
                                    ))
                                ) : (
                                    <div className="col-12">
                                        <div className="alert alert-info" role="alert">
                                            <i className="bi bi-info-circle me-2"></i>
                                            Tidak ada surah yang sesuai dengan pencarian Anda.
                                        </div>
                                    </div>
                                )}
                            </div>

                            {/* Summary */}
                            <div className="row mt-4">
                                <div className="col-12">
                                    <p className="surah-summary text-center">
                                        Menampilkan {filteredSurahs.length} dari {SURAHS.length} surah
                                    </p>
                                </div>
                            </div>
                        </div>
                    </section>
                </>
            }
        />
    );
}
```

## Ayah.jsx
```jsx
import { useEffect, useState, useRef } from "react";
import { useParams } from "react-router-dom";

import Layout from "../layout";
import Sidebar from "../layout/Sidebar";
import {
    PrimaryToggleButton,
    PrimaryOffcanvasButton,
} from "../layout";
import Brand from "../layout/Brand";
import Tajwid from "../helper/Tajwid";
import AyahService from "../services/ayah.service";

export default function AyahList() {
    const { surahNumber } = useParams();

    const [surah, setSurah] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState("");

    const audioRefs = useRef([]);
    const ayahRefs = useRef([]);

    useEffect(() => {
        const loadSurah = async () => {
            if (!surahNumber) return;

            try {
                setLoading(true);
                setError("");

                const result = await AyahService.getBySurah(surahNumber);

                if (!result?.data) {
                    throw new Error("Data surat tidak ditemukan.");
                }

                setSurah(result.data);

                // Reset refs ketika pindah surat
                audioRefs.current = [];
                ayahRefs.current = [];
            } catch (err) {
                console.error(err);

                setError(
                    err?.message ||
                        err?.error ||
                        "Terjadi kesalahan saat mengambil data surat."
                );
            } finally {
                setLoading(false);
            }
        };

        loadSurah();
    }, [surahNumber]);

    const stopAllAudio = () => {
        audioRefs.current.forEach((audio) => {
            if (!audio) return;

            audio.pause();
            audio.currentTime = 0;
        });
    };

    const handleAudioPlay = (currentIndex) => {
        audioRefs.current.forEach((audio, index) => {
            if (!audio || index === currentIndex) return;

            audio.pause();
            audio.currentTime = 0;
        });
    };

    const handleAudioEnded = (index) => {
        if (!surah?.ayat?.length) return;

        const nextIndex = index + 1;

        if (nextIndex >= surah.ayat.length) return;

        ayahRefs.current[nextIndex]?.scrollIntoView({
            behavior: "smooth",
            block: "center",
        });

        setTimeout(() => {
            audioRefs.current[nextIndex]?.play();
        }, 600);
    };

    const playAll = () => {
        if (!surah?.ayat?.length) return;

        stopAllAudio();

        ayahRefs.current[0]?.scrollIntoView({
            behavior: "smooth",
            block: "center",
        });

        audioRefs.current[0]?.play();
    };

    return (
        <Layout
            primaryMinWidth="calc(2.25rem + 1rem)"
            primarySidebarContent={<Sidebar />}
            mainContent={
                <>
                    <header className="position-sticky top-0 p-2 px-lg-4 z-3 bg-body border-bottom">
                        <div className="container">
                            <div className="d-flex align-items-center gap-2">
                                <PrimaryOffcanvasButton className="btn btn-light d-sm-none" />

                                <div className="d-lg-none">
                                    <Brand />
                                </div>

                                <PrimaryToggleButton className="btn-light ms-auto d-none d-sm-flex">
                                    <i className="bi bi-list"></i>
                                </PrimaryToggleButton>
                            </div>
                        </div>
                    </header>

                    <section className="py-4">
                        <div className="container">
                            {loading && (
                                <div className="text-center py-5">
                                    <div className="spinner-border text-primary" />
                                </div>
                            )}

                            {!loading && error && (
                                <div className="alert alert-danger">
                                    {error}
                                </div>
                            )}

                            {!loading && !error && surah && (
                                <>
                                    <div className="card rounded-4 border-0 shadow-sm mb-4">
                                        <div className="card-body text-center py-4">
                                            <h1
                                                className="font-thuluth display-4"
                                                dir="rtl"
                                            >
                                                {surah.nama}
                                            </h1>

                                            <h4>{surah.namaLatin}</h4>

                                            <p className="text-muted mb-1">
                                                {surah.arti}
                                            </p>

                                            <small className="text-muted">
                                                {surah.tempatTurun} •{" "}
                                                {surah.jumlahAyat} Ayat
                                            </small>

                                            <div className="mt-4">
                                                <button
                                                    className="btn btn-success"
                                                    onClick={playAll}
                                                >
                                                    <i className="bi bi-play-fill me-2"></i>
                                                    Putar Semua Ayat
                                                </button>
                                            </div>
                                        </div>
                                    </div>

                                    <div className="row g-3 justify-content-center">
                                        {surah.ayat.map((ayah, index) => (
                                            <div
                                                key={ayah.nomorAyat}
                                                ref={(el) =>
                                                    (ayahRefs.current[index] = el)
                                                }
                                                className="col-12 col-sm-11 col-md-10 col-lg-9 col-xl-8"
                                            >
                                                <div className="card rounded-4 border shadow-sm">
                                                    <div className="card-body">
                                                        <div className="mb-3">
                                                            <span className="badge bg-primary rounded-pill">
                                                                Ayat {ayah.nomorAyat}
                                                            </span>
                                                        </div>

                                                        <div
                                                            className="font-lpmq fs-1 text-end mb-4"
                                                            dir="rtl"
                                                            style={{
                                                                lineHeight: 2,
                                                            }}
                                                        >
                                                            <Tajwid
                                                                text={ayah.teksArab}
                                                            />
                                                        </div>

                                                        <div className="text-muted fst-italic mb-3">
                                                            {ayah.teksLatin}
                                                        </div>

                                                        <div className="mb-4">
                                                            {ayah.teksIndonesia}
                                                        </div>

                                                        <audio
                                                            controls
                                                            style={{
                                                                width: "100%",
                                                            }}
                                                            ref={(el) =>
                                                                (audioRefs.current[
                                                                    index
                                                                ] = el)
                                                            }
                                                            onPlay={() =>
                                                                handleAudioPlay(
                                                                    index
                                                                )
                                                            }
                                                            onEnded={() =>
                                                                handleAudioEnded(
                                                                    index
                                                                )
                                                            }
                                                        >
                                                            <source
                                                                src={
                                                                    ayah.audio?.[
                                                                        "02"
                                                                    ]
                                                                }
                                                                type="audio/mpeg"
                                                            />
                                                            Browser Anda tidak
                                                            mendukung audio.
                                                        </audio>
                                                    </div>
                                                </div>
                                            </div>
                                        ))}
                                    </div>
                                </>
                            )}
                        </div>
                    </section>
                </>
            }
        />
    );
}
```

## Api.js
```js
import axios from "axios";

const api = axios.create({
    baseURL: "https://equran.id/api/v2",
    headers: {
        "Content-Type": "application/json",
    },
    timeout: 10000,
});

export default api;
```

## ayah.services.js
```js
import api from "./api";

const AyahService = {
    async getBySurah(surahNumber) {
        try {
            const response = await api.get(`/surat/${surahNumber}`);
            return response.data;
        } catch (error) {
            throw error.response?.data || error;
        }
    },
};

export default AyahService;
```

## Tajwid.jsx
```jsx
import Layout from "../layout";
import Sidebar from "../layout/Sidebar";
import { PrimaryToggleButton, PrimaryOffcanvasButton } from "../layout";
import Brand from "../layout/Brand";
import { useState } from "react";
import Tajwid from "../hooks/Tajwid";

import { Capacitor } from "@capacitor/core";
import { Clipboard } from "@capacitor/clipboard";

const readClipboard = async () => {
    if (Capacitor.isNativePlatform()) {
        const { value } = await Clipboard.read();
        return value;
    }

    return await navigator.clipboard.readText();
};

const writeClipboard = async (text) => {
    if (Capacitor.isNativePlatform()) {
        await Clipboard.write({
            string: text,
        });
        return;
    }

    await navigator.clipboard.writeText(text);
};

export default function Tajwid() {
    const [text, setText] = useState("");

    const handlePaste = async () => {
        try {
            const clipboardText = await readClipboard();

            setText((prev) =>
                prev ? prev + "\n" + clipboardText : clipboardText
            );
        } catch (err) {
            console.error("Clipboard Error:", err);
            alert(
                err?.message ||
                JSON.stringify(err) ||
                "Gagal membaca clipboard"
            );
        }
    };

    const handleCopy = async () => {
        try {
            await writeClipboard(text);
            alert("Teks berhasil disalin");
        } catch {
            alert("Gagal menyalin teks");
        }
    };

    const handleClear = () => {
        if (window.confirm("Hapus seluruh teks?")) {
            setText("");
        }
    };

    return (
        <>
            <style>{`
                :root {
                    --primary-gradient: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
                    --secondary-gradient: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
                    --text-primary: #1a1a2e;
                    --text-secondary: #6c757d;
                    --border-color: #e8eef7;
                    --bg-light: #f8fafc;
                    --shadow-sm: 0 2px 8px rgba(0, 0, 0, 0.08);
                    --shadow-md: 0 8px 24px rgba(0, 0, 0, 0.12);
                    --shadow-lg: 0 12px 32px rgba(0, 0, 0, 0.15);
                }

                * {
                    --bs-border-color: var(--border-color);
                }

                header {
                    border-bottom: 1px solid var(--border-color);
                    backdrop-filter: blur(10px);
                    background: rgba(255, 255, 255, 0.95) !important;
                }

                .nav-tabs {
                    border: none;
                    gap: 0.5rem;
                    background: var(--bg-light);
                    padding: 0.75rem;
                    border-radius: 12px;
                }

                .nav-tabs .nav-link {
                    border: none;
                    color: var(--text-secondary);
                    font-weight: 500;
                    padding: 0.75rem 1.25rem;
                    border-radius: 8px;
                    transition: all 0.3s ease;
                    background: transparent;
                }

                .nav-tabs .nav-link:hover {
                    background: rgba(102, 126, 234, 0.1);
                    color: #667eea;
                }

                .nav-tabs .nav-link.active {
                    background: var(--primary-gradient);
                    color: white;
                    box-shadow: var(--shadow-sm);
                }

                .card {
                    border: 1px solid var(--border-color);
                    border-radius: 16px;
                    box-shadow: var(--shadow-sm);
                    transition: all 0.3s ease;
                    overflow: hidden;
                    background: white;
                }

                .card:hover {
                    box-shadow: var(--shadow-md);
                    border-color: #667eea;
                }

                .card-header {
                    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
                    color: white;
                    border: none;
                    font-weight: 600;
                    letter-spacing: 0.5px;
                    padding: 1.25rem 1.5rem;
                    font-size: 1.1rem;
                }

                .card-body {
                    padding: 2rem;
                }

                .form-control {
                    border: 2px solid var(--border-color);
                    border-radius: 12px;
                    padding: 1.25rem;
                    transition: all 0.3s ease;
                    color: var(--text-primary);
                }

                .form-control:focus {
                    border-color: #667eea;
                    box-shadow: 0 0 0 0.2rem rgba(102, 126, 234, 0.15);
                    color: var(--text-primary);
                }

                .form-control::placeholder {
                    color: #adb5bd;
                    opacity: 0.7;
                }

                .form-control[dir="rtl"] {
                    font-size: 1.5rem;
                    line-height: 2;
                }

                .output-box {
                    border: 2px solid var(--border-color);
                    border-radius: 12px;
                    padding: 2rem;
                    background: linear-gradient(
                        135deg,
                        rgba(248, 250, 252, 1) 0%,
                        rgba(240, 244, 255, 1) 100%
                    );
                    min-height: 250px;
                    transition: all 0.3s ease;
                }

                .output-box:hover {
                    border-color: #764ba2;
                }

                .toolbar {
                    display: flex;
                    gap: 0.5rem;
                    margin-bottom: 1rem;
                    flex-wrap: wrap;
                }

                .toolbar .btn {
                    border-radius: 10px;
                    font-weight: 500;
                }

                @media (max-width: 767px) {
                    .card-body {
                        padding: 1.5rem;
                    }

                    .form-control[dir="rtl"] {
                        font-size: 1.25rem;
                    }
                }

                html {
                    scroll-behavior: smooth;
                }

                .overflow-auto::-webkit-scrollbar {
                    width: 8px;
                    height: 8px;
                }

                .overflow-auto::-webkit-scrollbar-track {
                    background: var(--bg-light);
                    border-radius: 10px;
                }

                .overflow-auto::-webkit-scrollbar-thumb {
                    background: #667eea;
                    border-radius: 10px;
                }

                .overflow-auto::-webkit-scrollbar-thumb:hover {
                    background: #764ba2;
                }
            `}</style>

            <Layout
                primaryMinWidth="calc(2.25rem + 1rem)"
                primarySidebarContent={<Sidebar />}
                mainContent={
                    <>
                        <header className="position-sticky top-0 p-2 px-lg-4 z-3">
                            <div className="container">
                                <div className="row">
                                    <div className="col-12 d-flex gap-2 align-items-center">
                                        <PrimaryOffcanvasButton className="btn btn-light d-sm-none" />
                                        <div className="d-lg-none">
                                            <Brand />
                                        </div>
                                        <PrimaryToggleButton className="btn-light ms-auto d-none d-sm-flex">
                                            <i className="bi bi-list"></i>
                                        </PrimaryToggleButton>
                                    </div>
                                </div>
                            </div>
                        </header>

                        <section>
                            <div className="container">
                                <div className="row g-3">
                                    <div className="col-lg-6">
                                        <div className="card h-100">
                                            <div className="card-header">
                                                <i className="bi bi-pencil-fill me-2"></i>
                                                Input Teks Ayat
                                            </div>

                                            <div className="card-body">
                                                <div className="toolbar">
                                                    <button
                                                        type="button"
                                                        className="btn btn-primary"
                                                        onClick={handlePaste}
                                                    >
                                                        <i className="bi bi-clipboard-plus me-2"></i>
                                                        Paste
                                                    </button>

                                                    <button
                                                        type="button"
                                                        className="btn btn-success"
                                                        onClick={handleCopy}
                                                        disabled={!text}
                                                    >
                                                        <i className="bi bi-clipboard-check me-2"></i>
                                                        Copy
                                                    </button>

                                                    <button
                                                        type="button"
                                                        className="btn btn-danger"
                                                        onClick={handleClear}
                                                        disabled={!text}
                                                    >
                                                        <i className="bi bi-trash me-2"></i>
                                                        Clear
                                                    </button>
                                                </div>

                                                <textarea
                                                    className="form-control font-lpmq text-end"
                                                    rows={14}
                                                    dir="rtl"
                                                    value={text}
                                                    onChange={(e) => setText(e.target.value)}
                                                    placeholder="أدخل النص القرآني..."
                                                    style={{
                                                        fontSize: "1.5rem",
                                                        lineHeight: "2",
                                                        minHeight: "450px"
                                                    }}
                                                />
                                            </div>
                                        </div>
                                    </div>

                                    <div className="col-lg-6">
                                        <div className="card h-100">
                                            <div className="card-header">
                                                <i className="bi bi-eye-fill me-2"></i>
                                                Output Tajwid
                                            </div>

                                            <div className="card-body overflow-auto">
                                                <p
                                                    dir="rtl"
                                                    className="font-lpmq text-end fs-4"
                                                    style={{
                                                        fontSize: "1.5rem",
                                                        lineHeight: "2.2",
                                                        color: "#1a1a2e",
                                                        minHeight: "450px",
                                                        paddingRight: "1rem"
                                                    }}
                                                >
                                                    <Tajwid text={text} />
                                                </p>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div className="row mt-3">
                                    <div className="col-12">
                                        <TajwidLegend />
                                    </div>
                                </div>
                            </div>
                        </section>
                    </>
                }
            />
        </>
    );
}

export function TajwidLegend() {
    return (
        <div className="card shadow-sm">
            <div className="card-body">
                <h5 className="card-title mb-4">
                    Keterangan Hukum Tajwid
                </h5>

                <div className="d-flex align-items-start gap-3 mb-3">
                    <span
                        style={{
                            width: "18px",
                            height: "18px",
                            backgroundColor: "rgb(179, 74, 0)",
                            borderRadius: "4px",
                            flexShrink: 0,
                            marginTop: "4px"
                        }}
                    />
                    <div>
                        <strong style={{ color: "rgb(179, 74, 0)" }}>
                            Izhar Halqi
                        </strong>
                        <br />
                        Apabila nun sukun (
                        <span className="font-lpmq">نْ</span>
                        ) atau tanwin (
                        <span className="font-lpmq">ً ٍ ٌ</span>
                        ) bertemu dengan salah satu huruf{" "}
                        <span className="font-lpmq">
                            ء هـ ع ح غ خ
                        </span>
                        , maka dibaca jelas dan tidak berdengung.
                    </div>
                </div>

                <div className="d-flex align-items-start gap-3 mb-3">
                    <span
                        style={{
                            width: "18px",
                            height: "18px",
                            backgroundColor: "rgb(0, 128, 0)",
                            borderRadius: "4px",
                            flexShrink: 0,
                            marginTop: "4px"
                        }}
                    />
                    <div>
                        <strong style={{ color: "rgb(0, 128, 0)" }}>
                            Idgham Bighunnah
                        </strong>
                        <br />
                        Apabila nun sukun (
                        <span className="font-lpmq">نْ</span>
                        ) atau tanwin (
                        <span className="font-lpmq">ً ٍ ٌ</span>
                        ) bertemu dengan salah satu huruf{" "}
                        <span className="font-lpmq">
                            ي ن م و
                        </span>
                        , maka dibaca dengan dimasukkan (melebur) dan
                        berdengung selama 2–3 harakat.
                    </div>
                </div>

                <div className="d-flex align-items-start gap-3 mb-3">
                    <span
                        style={{
                            width: "18px",
                            height: "18px",
                            backgroundColor: "rgb(189, 15, 30)",
                            borderRadius: "4px",
                            flexShrink: 0,
                            marginTop: "4px"
                        }}
                    />
                    <div>
                        <strong style={{ color: "rgb(189, 15, 30)" }}>
                            Ikhfa Haqiqi
                        </strong>
                        <br />
                        Apabila nun sukun (
                        <span className="font-lpmq">نْ</span>
                        ) atau tanwin (
                        <span className="font-lpmq">ً ٍ ٌ</span>
                        ) bertemu dengan salah satu huruf{" "}
                        <span className="font-lpmq">
                            ت ث ج د ذ ز س ش ص ض ط ظ ف ق ك
                        </span>
                        , maka dibaca samar dan berdengung selama 2–3
                        harakat.
                    </div>
                </div>

                <div className="d-flex align-items-start gap-3 mb-3">
                    <span
                        style={{
                            width: "18px",
                            height: "18px",
                            backgroundColor: "rgb(0, 128, 128)",
                            borderRadius: "4px",
                            flexShrink: 0,
                            marginTop: "4px"
                        }}
                    />
                    <div>
                        <strong style={{ color: "rgb(0, 128, 128)" }}>
                            Iqlab
                        </strong>
                        <br />
                        Apabila nun sukun (
                        <span className="font-lpmq">نْ</span>
                        ) atau tanwin (
                        <span className="font-lpmq">ً ٍ ٌ</span>
                        ) bertemu dengan huruf{" "}
                        <span className="font-lpmq">ب</span>,
                        maka bunyi nun berubah menjadi mim (
                        <span className="font-lpmq">م</span>
                        ) dan dibaca dengan dengung selama 2–3 harakat.
                    </div>
                </div>

                <div className="d-flex align-items-start gap-3">
                    <span
                        style={{
                            width: "18px",
                            height: "18px",
                            backgroundColor: "rgb(129, 0, 194)",
                            borderRadius: "4px",
                            flexShrink: 0,
                            marginTop: "4px"
                        }}
                    />
                    <div>
                        <strong style={{ color: "rgb(129, 0, 194)" }}>
                            Mad Thabi'i
                        </strong>
                        <br />
                        Mad asli yang terdapat dalam satu kata dan dibaca
                        panjang <strong>2 harakat (1 alif)</strong>.
                    </div>
                </div>
            </div>
        </div>
    );
}
```

## utils/Surah.jsx
```jsx
const SURAHS = [
    {
        "number": 1,
        "name": "Al-Fātiḥah",
        "translation": "Pembuka",
        "type": "Makkiyah",
        "ayahCount": "7 Ayat",
        "arabic": "الفاتحة"
    },
    {
        "number": 2,
        "name": "Al-Baqarah",
        "translation": "Sapi",
        "type": "Madaniyah",
        "ayahCount": "286 Ayat",
        "arabic": "البقرة"
    },
    {
        "number": 3,
        "name": "Āli ‘Imrān",
        "translation": "Keluarga Imran",
        "type": "Madaniyah",
        "ayahCount": "200 Ayat",
        "arabic": "اٰل عمرٰن"
    },
    {
        "number": 4,
        "name": "An-Nisā'",
        "translation": "Perempuan",
        "type": "Madaniyah",
        "ayahCount": "176 Ayat",
        "arabic": "النّساۤء"
    },
    {
        "number": 5,
        "name": "Al-Mā'idah",
        "translation": "Hidangan",
        "type": "Madaniyah",
        "ayahCount": "120 Ayat",
        "arabic": "الماۤئدة"
    },
    {
        "number": 6,
        "name": "Al-An‘ām",
        "translation": "Binatang Ternak",
        "type": "Makkiyah",
        "ayahCount": "165 Ayat",
        "arabic": "الانعام"
    },
    {
        "number": 7,
        "name": "Al-A‘rāf",
        "translation": "Tempat Tertinggi",
        "type": "Makkiyah",
        "ayahCount": "206 Ayat",
        "arabic": "الاعراف"
    },
    {
        "number": 8,
        "name": "Al-Anfāl",
        "translation": "Rampasan Perang",
        "type": "Madaniyah",
        "ayahCount": "75 Ayat",
        "arabic": "الانفال"
    },
    {
        "number": 9,
        "name": "At-Taubah",
        "translation": "Pengampunan",
        "type": "Madaniyah",
        "ayahCount": "129 Ayat",
        "arabic": "التّوبة"
    },
    {
        "number": 10,
        "name": "Yūnus",
        "translation": "Yunus",
        "type": "Makkiyah",
        "ayahCount": "109 Ayat",
        "arabic": "يونس"
    },
    {
        "number": 11,
        "name": "Hūd",
        "translation": "Hud",
        "type": "Makkiyah",
        "ayahCount": "123 Ayat",
        "arabic": "هود"
    },
    {
        "number": 12,
        "name": "Yūsuf",
        "translation": "Yusuf",
        "type": "Makkiyah",
        "ayahCount": "111 Ayat",
        "arabic": "يوسف"
    },
    {
        "number": 13,
        "name": "Ar-Ra‘d",
        "translation": "Guruh",
        "type": "Makkiyah",
        "ayahCount": "43 Ayat",
        "arabic": "الرّعد"
    },
    {
        "number": 14,
        "name": "Ibrāhīm",
        "translation": "Ibrahim",
        "type": "Makkiyah",
        "ayahCount": "52 Ayat",
        "arabic": "ابرٰهيم"
    },
    {
        "number": 15,
        "name": "Al-Ḥijr",
        "translation": "Hijr",
        "type": "Makkiyah",
        "ayahCount": "99 Ayat",
        "arabic": "الحجر"
    },
    {
        "number": 16,
        "name": "An-Naḥl",
        "translation": "Lebah",
        "type": "Makkiyah",
        "ayahCount": "128 Ayat",
        "arabic": "النّحل"
    },
    {
        "number": 17,
        "name": "Al-Isrā'",
        "translation": "Memperjalankan di Malam Hari",
        "type": "Makkiyah",
        "ayahCount": "111 Ayat",
        "arabic": "الاسراۤء"
    },
    {
        "number": 18,
        "name": "Al-Kahf",
        "translation": "Gua",
        "type": "Makkiyah",
        "ayahCount": "110 Ayat",
        "arabic": "الكهف"
    },
    {
        "number": 19,
        "name": "Maryam",
        "translation": "Maryam",
        "type": "Makkiyah",
        "ayahCount": "98 Ayat",
        "arabic": "مريم"
    },
    {
        "number": 20,
        "name": "Ṭāhā",
        "translation": "Taha",
        "type": "Makkiyah",
        "ayahCount": "135 Ayat",
        "arabic": "طٰهٰ"
    },
    {
        "number": 21,
        "name": "Al-Anbiyā'",
        "translation": "Para Nabi",
        "type": "Makkiyah",
        "ayahCount": "112 Ayat",
        "arabic": "الانبياۤء"
    },
    {
        "number": 22,
        "name": "Al-Ḥajj",
        "translation": "Haji",
        "type": "Madaniyah",
        "ayahCount": "78 Ayat",
        "arabic": "الحجّ"
    },
    {
        "number": 23,
        "name": "Al-Mu'minūn",
        "translation": "Orang-Orang Mukmin",
        "type": "Makkiyah",
        "ayahCount": "118 Ayat",
        "arabic": "المؤمنون"
    },
    {
        "number": 24,
        "name": "An-Nūr",
        "translation": "Cahaya",
        "type": "Madaniyah",
        "ayahCount": "64 Ayat",
        "arabic": "النّور"
    },
    {
        "number": 25,
        "name": "Al-Furqān",
        "translation": "Pembeda",
        "type": "Makkiyah",
        "ayahCount": "77 Ayat",
        "arabic": "الفرقان"
    },
    {
        "number": 26,
        "name": "Asy-Syu‘arā'",
        "translation": "Para Penyair",
        "type": "Makkiyah",
        "ayahCount": "227 Ayat",
        "arabic": "الشّعراۤء"
    },
    {
        "number": 27,
        "name": "An-Naml",
        "translation": "Semut",
        "type": "Makkiyah",
        "ayahCount": "93 Ayat",
        "arabic": "النّمل"
    },
    {
        "number": 28,
        "name": "Al-Qaṣaṣ",
        "translation": "Kisah-Kisah",
        "type": "Makkiyah",
        "ayahCount": "88 Ayat",
        "arabic": "القصص"
    },
    {
        "number": 29,
        "name": "Al-‘Ankabūt",
        "translation": "Laba-Laba",
        "type": "Makkiyah",
        "ayahCount": "69 Ayat",
        "arabic": "العنكبوت"
    },
    {
        "number": 30,
        "name": "Ar-Rūm",
        "translation": "Romawi",
        "type": "Makkiyah",
        "ayahCount": "60 Ayat",
        "arabic": "الرّوم"
    },
    {
        "number": 31,
        "name": "Luqmān",
        "translation": "Luqman",
        "type": "Makkiyah",
        "ayahCount": "34 Ayat",
        "arabic": "لقمٰن"
    },
    {
        "number": 32,
        "name": "As-Sajdah",
        "translation": "Sajdah",
        "type": "Makkiyah",
        "ayahCount": "30 Ayat",
        "arabic": "السّجدة"
    },
    {
        "number": 33,
        "name": "Al-Aḥzāb",
        "translation": "Golongan Yang Bersekutu",
        "type": "Madaniyah",
        "ayahCount": "73 Ayat",
        "arabic": "الاحزاب"
    },
    {
        "number": 34,
        "name": "Saba'",
        "translation": "Saba'",
        "type": "Makkiyah",
        "ayahCount": "54 Ayat",
        "arabic": "سبأ"
    },
    {
        "number": 35,
        "name": "Fāṭir",
        "translation": "Pencipta",
        "type": "Makkiyah",
        "ayahCount": "45 Ayat",
        "arabic": "فاطر"
    },
    {
        "number": 36,
        "name": "Yāsīn",
        "translation": "Yasin",
        "type": "Makkiyah",
        "ayahCount": "83 Ayat",
        "arabic": "يٰسۤ"
    },
    {
        "number": 37,
        "name": "Aṣ-Ṣāffāt",
        "translation": "Barisan-Barisan",
        "type": "Makkiyah",
        "ayahCount": "182 Ayat",
        "arabic": "الصّٰۤفّٰت"
    },
    {
        "number": 38,
        "name": "Ṣād",
        "translation": "Ṣād",
        "type": "Makkiyah",
        "ayahCount": "88 Ayat",
        "arabic": "صۤ"
    },
    {
        "number": 39,
        "name": "Az-Zumar",
        "translation": "Rombongan",
        "type": "Makkiyah",
        "ayahCount": "75 Ayat",
        "arabic": "الزّمر"
    },
    {
        "number": 40,
        "name": "Gāfir",
        "translation": "Maha Pengampun",
        "type": "Makkiyah",
        "ayahCount": "85 Ayat",
        "arabic": "غافر"
    },
    {
        "number": 41,
        "name": "Fuṣṣilat",
        "translation": "Dijelaskan",
        "type": "Makkiyah",
        "ayahCount": "54 Ayat",
        "arabic": "فصّلت"
    },
    {
        "number": 42,
        "name": "Asy-Syūrā",
        "translation": "Musyawarah",
        "type": "Makkiyah",
        "ayahCount": "53 Ayat",
        "arabic": "الشّورٰى"
    },
    {
        "number": 43,
        "name": "Az-Zukhruf",
        "translation": "Perhiasan dari Emas",
        "type": "Makkiyah",
        "ayahCount": "89 Ayat",
        "arabic": "الزّخرف"
    },
    {
        "number": 44,
        "name": "Ad-Dukhān",
        "translation": "Kabut Asap",
        "type": "Makkiyah",
        "ayahCount": "59 Ayat",
        "arabic": "الدّخان"
    },
    {
        "number": 45,
        "name": "Al-Jāṡiyah",
        "translation": "Berlutut",
        "type": "Makkiyah",
        "ayahCount": "37 Ayat",
        "arabic": "الجاثية"
    },
    {
        "number": 46,
        "name": "Al-Aḥqāf",
        "translation": "Ahqaf",
        "type": "Makkiyah",
        "ayahCount": "35 Ayat",
        "arabic": "الاحقاف"
    },
    {
        "number": 47,
        "name": "Muḥammad",
        "translation": "Nabi Muhammad",
        "type": "Madaniyah",
        "ayahCount": "38 Ayat",
        "arabic": "محمّد"
    },
    {
        "number": 48,
        "name": "Al-Fatḥ",
        "translation": "Kemenangan",
        "type": "Madaniyah",
        "ayahCount": "29 Ayat",
        "arabic": "الفتح"
    },
    {
        "number": 49,
        "name": "Al-Ḥujurāt",
        "translation": "Kamar-Kamar",
        "type": "Madaniyah",
        "ayahCount": "18 Ayat",
        "arabic": "الحجرٰت"
    },
    {
        "number": 50,
        "name": "Qāf",
        "translation": "Qaf",
        "type": "Makkiyah",
        "ayahCount": "45 Ayat",
        "arabic": "قۤ"
    },
    {
        "number": 51,
        "name": "Aż-Żāriyāt",
        "translation": "Yang Menerbangkan",
        "type": "Makkiyah",
        "ayahCount": "60 Ayat",
        "arabic": "الذّٰريٰت"
    },
    {
        "number": 52,
        "name": "Aṭ-Ṭūr",
        "translation": "Gunung",
        "type": "Makkiyah",
        "ayahCount": "49 Ayat",
        "arabic": "الطّور"
    },
    {
        "number": 53,
        "name": "An-Najm",
        "translation": "Bintang",
        "type": "Makkiyah",
        "ayahCount": "62 Ayat",
        "arabic": "النّجم"
    },
    {
        "number": 54,
        "name": "Al-Qamar",
        "translation": "Bulan",
        "type": "Makkiyah",
        "ayahCount": "55 Ayat",
        "arabic": "القمر"
    },
    {
        "number": 55,
        "name": "Ar-Raḥmān",
        "translation": "Yang Maha Pengasih",
        "type": "Makkiyah",
        "ayahCount": "78 Ayat",
        "arabic": "الرّحمٰن"
    },
    {
        "number": 56,
        "name": "Al-Wāqi‘ah",
        "translation": "Hari Kiamat Yang Pasti Terjadi",
        "type": "Makkiyah",
        "ayahCount": "96 Ayat",
        "arabic": "الواقعة"
    },
    {
        "number": 57,
        "name": "Al-Ḥadīd",
        "translation": "Besi",
        "type": "Madaniyah",
        "ayahCount": "29 Ayat",
        "arabic": "الحديد"
    },
    {
        "number": 58,
        "name": "Al-Mujādalah",
        "translation": "Gugatan",
        "type": "Madaniyah",
        "ayahCount": "22 Ayat",
        "arabic": "المجادلة"
    },
    {
        "number": 59,
        "name": "Al-Ḥasyr",
        "translation": "Pengusiran",
        "type": "Madaniyah",
        "ayahCount": "24 Ayat",
        "arabic": "الحشر"
    },
    {
        "number": 60,
        "name": "Al-Mumtaḥanah",
        "translation": "Wanita Yang Diuji",
        "type": "Madaniyah",
        "ayahCount": "13 Ayat",
        "arabic": "الممتحنة"
    },
    {
        "number": 61,
        "name": "Aṣ-Ṣaff",
        "translation": "Barisan",
        "type": "Madaniyah",
        "ayahCount": "14 Ayat",
        "arabic": "الصّفّ"
    },
    {
        "number": 62,
        "name": "Al-Jumu‘ah",
        "translation": "Jumat",
        "type": "Madaniyah",
        "ayahCount": "11 Ayat",
        "arabic": "الجمعة"
    },
    {
        "number": 63,
        "name": "Al-Munāfiqūn",
        "translation": "Orang-Orang Munafik",
        "type": "Madaniyah",
        "ayahCount": "11 Ayat",
        "arabic": "المنٰفقون"
    },
    {
        "number": 64,
        "name": "At-Tagābun",
        "translation": "Pengungkapan Kesalahan",
        "type": "Madaniyah",
        "ayahCount": "18 Ayat",
        "arabic": "التّغابن"
    },
    {
        "number": 65,
        "name": "Aṭ-Ṭalāq",
        "translation": "Talak",
        "type": "Madaniyah",
        "ayahCount": "12 Ayat",
        "arabic": "الطّلاق"
    },
    {
        "number": 66,
        "name": "At-taḥrīm",
        "translation": "Pengharaman",
        "type": "Madaniyah",
        "ayahCount": "12 Ayat",
        "arabic": "التّحريم"
    },
    {
        "number": 67,
        "name": "Al-Mulk",
        "translation": "Kerajaan",
        "type": "Makkiyah",
        "ayahCount": "30 Ayat",
        "arabic": "المُلك"
    },
    {
        "number": 68,
        "name": "Al-Qalam",
        "translation": "Pena",
        "type": "Makkiyah",
        "ayahCount": "52 Ayat",
        "arabic": "القلم"
    },
    {
        "number": 69,
        "name": "Al-Ḥāqqah",
        "translation": "Hari Kiamat Yang Pasti Terjadi",
        "type": "Makkiyah",
        "ayahCount": "52 Ayat",
        "arabic": "الحاۤقّة"
    },
    {
        "number": 70,
        "name": "Al-Ma‘ārij",
        "translation": "Tempat-Tempat Naik",
        "type": "Makkiyah",
        "ayahCount": "44 Ayat",
        "arabic": "المعارج"
    },
    {
        "number": 71,
        "name": "Nūḥ",
        "translation": "Nuh",
        "type": "Makkiyah",
        "ayahCount": "28 Ayat",
        "arabic": "نوح"
    },
    {
        "number": 72,
        "name": "Al-Jinn",
        "translation": "Jin",
        "type": "Makkiyah",
        "ayahCount": "28 Ayat",
        "arabic": "الجنّ"
    },
    {
        "number": 73,
        "name": "Al-Muzzammil",
        "translation": "Orang Berkelumun",
        "type": "Makkiyah",
        "ayahCount": "20 Ayat",
        "arabic": "المزّمّل"
    },
    {
        "number": 74,
        "name": "Al-Muddaṡṡir",
        "translation": "Orang Berselimut",
        "type": "Makkiyah",
        "ayahCount": "56 Ayat",
        "arabic": "المدّثّر"
    },
    {
        "number": 75,
        "name": "Al-Qiyāmah",
        "translation": "Hari Kiamat",
        "type": "Makkiyah",
        "ayahCount": "40 Ayat",
        "arabic": "القيٰمة"
    },
    {
        "number": 76,
        "name": "Al-Insān",
        "translation": "Manusia",
        "type": "Madaniyah",
        "ayahCount": "31 Ayat",
        "arabic": "الانسان"
    },
    {
        "number": 77,
        "name": "Al-Mursalāt",
        "translation": "Malaikat Yang Diutus",
        "type": "Makkiyah",
        "ayahCount": "50 Ayat",
        "arabic": "المرسلٰت"
    },
    {
        "number": 78,
        "name": "An-Naba'",
        "translation": "Berita",
        "type": "Makkiyah",
        "ayahCount": "40 Ayat",
        "arabic": "النّبأ"
    },
    {
        "number": 79,
        "name": "An-Nāzi‘āt",
        "translation": "Yang Mencabut Dengan Keras",
        "type": "Makkiyah",
        "ayahCount": "46 Ayat",
        "arabic": "النّٰزعٰت"
    },
    {
        "number": 80,
        "name": "‘Abasa",
        "translation": "Berwajah Masam",
        "type": "Makkiyah",
        "ayahCount": "42 Ayat",
        "arabic": "عبس"
    },
    {
        "number": 81,
        "name": "At-Takwīr",
        "translation": "Penggulungan",
        "type": "Makkiyah",
        "ayahCount": "29 Ayat",
        "arabic": "التّكوير"
    },
    {
        "number": 82,
        "name": "Al-Infiṭār",
        "translation": "Terbelah",
        "type": "Makkiyah",
        "ayahCount": "19 Ayat",
        "arabic": "الانفطار"
    },
    {
        "number": 83,
        "name": "Al-Muṭaffifīn",
        "translation": "Orang-Orang Yang Curang",
        "type": "Makkiyah",
        "ayahCount": "36 Ayat",
        "arabic": "المطفّفين"
    },
    {
        "number": 84,
        "name": "Al-Insyiqāq",
        "translation": "Terbelah",
        "type": "Makkiyah",
        "ayahCount": "25 Ayat",
        "arabic": "الانشقاق"
    },
    {
        "number": 85,
        "name": "Al-Burūj",
        "translation": "Gugusan Bintang",
        "type": "Makkiyah",
        "ayahCount": "22 Ayat",
        "arabic": "البروج"
    },
    {
        "number": 86,
        "name": "Aṭ-Ṭāriq",
        "translation": "Yang Datang Pada Malam Hari",
        "type": "Makkiyah",
        "ayahCount": "17 Ayat",
        "arabic": "الطّارق"
    },
    {
        "number": 87,
        "name": "Al-A‘lā",
        "translation": "Yang Maha Tinggi",
        "type": "Makkiyah",
        "ayahCount": "19 Ayat",
        "arabic": "الاعلى"
    },
    {
        "number": 88,
        "name": "Al-Gāsyiyah",
        "translation": "Hari Kiamat Yang Menghilangkan Kesadaran",
        "type": "Makkiyah",
        "ayahCount": "26 Ayat",
        "arabic": "الغاشية"
    },
    {
        "number": 89,
        "name": "Al-Fajr",
        "translation": "Fajar",
        "type": "Makkiyah",
        "ayahCount": "30 Ayat",
        "arabic": "الفجر"
    },
    {
        "number": 90,
        "name": "Al-Balad",
        "translation": "Negeri",
        "type": "Makkiyah",
        "ayahCount": "20 Ayat",
        "arabic": "البلد"
    },
    {
        "number": 91,
        "name": "Asy-Syams",
        "translation": "Matahari",
        "type": "Makkiyah",
        "ayahCount": "15 Ayat",
        "arabic": "الشّمس"
    },
    {
        "number": 92,
        "name": "Al-Lail",
        "translation": "Malam",
        "type": "Makkiyah",
        "ayahCount": "21 Ayat",
        "arabic": "الّيل"
    },
    {
        "number": 93,
        "name": "Aḍ-Ḍuḥā",
        "translation": "Duha",
        "type": "Makkiyah",
        "ayahCount": "11 Ayat",
        "arabic": "الضّحى"
    },
    {
        "number": 94,
        "name": "Asy-Syarḥ",
        "translation": "Pelapangan",
        "type": "Makkiyah",
        "ayahCount": "8 Ayat",
        "arabic": "الشّرح"
    },
    {
        "number": 95,
        "name": "At-Tīn",
        "translation": "Buah Tin",
        "type": "Makkiyah",
        "ayahCount": "8 Ayat",
        "arabic": "التّين"
    },
    {
        "number": 96,
        "name": "Al-‘Alaq",
        "translation": "Segumpal Darah",
        "type": "Makkiyah",
        "ayahCount": "19 Ayat",
        "arabic": "العلق"
    },
    {
        "number": 97,
        "name": "Al-Qadr",
        "translation": "Al-Qadar",
        "type": "Makkiyah",
        "ayahCount": "5 Ayat",
        "arabic": "القدر"
    },
    {
        "number": 98,
        "name": "Al-Bayyinah",
        "translation": "Bukti Nyata",
        "type": "Madaniyah",
        "ayahCount": "8 Ayat",
        "arabic": "البيّنة"
    },
    {
        "number": 99,
        "name": "Az-Zalzalah",
        "translation": "Guncangan",
        "type": "Madaniyah",
        "ayahCount": "8 Ayat",
        "arabic": "الزّلزلة"
    },
    {
        "number": 100,
        "name": "Al-‘Ādiyāt",
        "translation": "Kuda Perang Yang Berlari Kencang",
        "type": "Makkiyah",
        "ayahCount": "11 Ayat",
        "arabic": "العٰديٰت"
    },
    {
        "number": 101,
        "name": "Al-Qāri‘ah",
        "translation": "Al-Qāri‘ah",
        "type": "Makkiyah",
        "ayahCount": "11 Ayat",
        "arabic": "القارعة"
    },
    {
        "number": 102,
        "name": "At-Takāṡur",
        "translation": "Berbangga-Bangga Dalam Memperbanyak Dunia",
        "type": "Makkiyah",
        "ayahCount": "8 Ayat",
        "arabic": "التّكاثر"
    },
    {
        "number": 103,
        "name": "Al-‘Aṣr",
        "translation": "Masa",
        "type": "Makkiyah",
        "ayahCount": "3 Ayat",
        "arabic": "العصر"
    },
    {
        "number": 104,
        "name": "Al-Humazah",
        "translation": "Pengumpat",
        "type": "Makkiyah",
        "ayahCount": "9 Ayat",
        "arabic": "الهمزة"
    },
    {
        "number": 105,
        "name": "Al-Fīl",
        "translation": "Gajah",
        "type": "Makkiyah",
        "ayahCount": "5 Ayat",
        "arabic": "الفيل"
    },
    {
        "number": 106,
        "name": "Quraisy",
        "translation": "Orang Quraisy",
        "type": "Makkiyah",
        "ayahCount": "4 Ayat",
        "arabic": "قريش"
    },
    {
        "number": 107,
        "name": "Al-Mā‘ūn",
        "translation": "Bantuan",
        "type": "Makkiyah",
        "ayahCount": "7 Ayat",
        "arabic": "الماعون"
    },
    {
        "number": 108,
        "name": "Al-Kauṡar",
        "translation": "Nikmat Yang Banyak",
        "type": "Makkiyah",
        "ayahCount": "3 Ayat",
        "arabic": "الكوثر"
    },
    {
        "number": 109,
        "name": "Al-Kāfirūn",
        "translation": "Orang-Orang kafir",
        "type": "Makkiyah",
        "ayahCount": "6 Ayat",
        "arabic": "الكٰفرون"
    },
    {
        "number": 110,
        "name": "An-Naṣr",
        "translation": "Pertolongan",
        "type": "Madaniyah",
        "ayahCount": "3 Ayat",
        "arabic": "النّصر"
    },
    {
        "number": 111,
        "name": "Al-Lahab",
        "translation": "Gejolak Api",
        "type": "Makkiyah",
        "ayahCount": "5 Ayat",
        "arabic": "اللّهب"
    },
    {
        "number": 112,
        "name": "Al-Ikhlāṣ",
        "translation": "Ikhlas",
        "type": "Makkiyah",
        "ayahCount": "4 Ayat",
        "arabic": "الاخلاص"
    },
    {
        "number": 113,
        "name": "Al-Falaq",
        "translation": "Fajar",
        "type": "Madaniyah",
        "ayahCount": "5 Ayat",
        "arabic": "الفلق"
    },
    {
        "number": 114,
        "name": "An-Nās",
        "translation": "Manusia",
        "type": "Madaniyah",
        "ayahCount": "6 Ayat",
        "arabic": "النّاس"
    }
];

export default SURAHS;
```

