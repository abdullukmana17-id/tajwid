# Dokumentasi
## Persiapan
- Install VsCode
- Install Node.js
- Install Terminal di microsoft store

## Instalasi Project React
- Buat Folder untuk menyimpan project
- Masuk ke dalam folder tersebut
- Klik kanan
- Kemudian Pilih `Open in Terminal`
- Kemudian ketik
```bash
npm create vite@latest
```
- Tulis nama project anda pada `Peoject Name`
- Pilih `React` pada bagian `Select a Framework`
- Pilih `Javascript` pada bagian `Select a variant`
- Pilih `Exlint` atau `Eslint` sesuai kebutuhan pada bagian `Which linter to use?`
- Pilih `Yes` pada bagian `Install with npm and start now?`
- Tunggu proses instalasi
- Proses instalasi selesai jika muncul
```bash
VITE v8.1.5  ready in 295 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
```
- Kemudian kunjungi [http://localhost:5173/](http://localhost:5173/) di browser seperti chrome

## Install Dependensi
- Buka tab baru pada terminal
- Akses folder project
```bash
cd "[Lokasi project]"
```
Contoh:
```bash
cd "C:\project\app"
```
- Kemudian install `react-router-dom` lewat terminal
```bash
npm install react-router-dom
```

- Install animasi AOS
```bash
npm install aos
```

- Kemudian install `scss`
```bash
npm install sass
```

- Install axios
```bash
npm install axios
```

- Install `axios`
```bash
npm install autosize
```

- Install Capacitor Core
```bash
npm install @capacitor/core
```

- Install Clipboard Plugin
```bash
npm install @capacitor/clipboard
```

- Instal plugin App
```bash
npm install @capacitor/app
```


## Mengatur Struktur Folder dan File Project
- Buka vscode
- Buka folder project yang telah dibuat
- Struktur folder yang harusnya muncul di vscode
```bash
nama_project/
├── node_modules/
├── public/
├── src/
├── .gitgnore
├── index.html
├── package-lock.json
├── package.json
├── README.md
├── vite.config.js
```
- Buat folder dan file yang sesuai dengan struktur berikut:
```bash
src/
├── assets/
│   ├── fonts/
├── components/
│   ├── Textarea.jsx
│   ├── Input.jsx
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
│   ├── surah.services.js
├── hooks/
│   ├── useAndroidBackButton.jsx
│   ├── Tajwid.jsx
├── utils/
│   ├── Surah.jsx
├── styles/
│   ├── index.scss
│   ├── header.scss
│   ├── footer.scss
│   ├── nav.scss
│   ├── sidebar.scss
│   ├── surah.scss
│   ├── ayah.scss
│   ├── home.scss
│   ├── editor.scss
│   ├── variables.scss
│   ├── layout.scss
│   ├── brand.scss
├── App.jsx
└── main.jsx
```

## Pembuatan layout dengan sidebar pada project
- Salin kode berikut pada `src/layouts/index.jsx`
```jsx
import {
  useState,
  useEffect,
  useRef,
  useCallback,
  createContext,
  useContext,
} from "react";
import { useLocation } from "react-router-dom";
import { Outlet } from "react-router-dom";

const KEY_PRIMARY = "layout:primarySidebarHidden";
const KEY_SECONDARY = "layout:secondarySidebarHidden";

function readStorage(key, fallback) {
  try {
    const raw = localStorage.getItem(key);
    return raw !== null ? JSON.parse(raw) : fallback;
  } catch {
    return fallback;
  }
}

function writeStorage(key, value) {
  try {
    localStorage.setItem(key, JSON.stringify(value));
  } catch {
    /* quota exceeded atau private browsing — diam saja */
  }
}

const SidebarLayoutContext = createContext(null);
export function useSidebarLayout() {
  const ctx = useContext(SidebarLayoutContext);
  if (!ctx) {
    throw new Error("useSidebarLayout harus digunakan di dalam <SidebarLayout>.");
  }
  return ctx;
}

export function PrimaryToggleButton({ children, className = "", ...props }) {
  const { togglePrimary, primaryHidden } = useSidebarLayout();
  return (
    <button
      type="button"
      className={`btn ${className}`}
      onClick={togglePrimary}
      aria-label="Toggle sidebar kiri"
      aria-expanded={!primaryHidden}
      {...props}
    >
      {children ?? <i className="bi bi-layout-sidebar" />}
    </button>
  );
}

export function SecondaryToggleButton({ children, className = "", ...props }) {
  const { toggleSecondary, secondaryHidden } = useSidebarLayout();
  return (
    <button
      type="button"
      className={`btn ${className}`}
      onClick={toggleSecondary}
      aria-label="Toggle sidebar kanan"
      aria-expanded={!secondaryHidden}
      {...props}
    >
      {children ?? <i className="bi bi-layout-sidebar-reverse" />}
    </button>
  );
}

export function PrimaryOffcanvasButton({ children, className = "", ...props }) {
  return (
    <button
      type="button"
      className={`btn ${className}`}
      data-bs-toggle="offcanvas"
      data-bs-target="#sl-primarySidebar"
      aria-controls="sl-primarySidebar"
      aria-expanded="false"
      aria-label="Buka sidebar kiri"
      {...props}
    >
      {children ?? <i className="bi bi-layout-sidebar-inset" />}
    </button>
  );
}

export function SecondaryOffcanvasButton({ children, className = "", ...props }) {
  return (
    <button
      type="button"
      className={`btn ${className}`}
      data-bs-toggle="offcanvas"
      data-bs-target="#sl-secondarySidebar"
      aria-controls="sl-secondarySidebar"
      aria-expanded="false"
      aria-label="Buka sidebar kanan"
      {...props}
    >
      {children ?? <i className="bi bi-layout-sidebar-inset-reverse" />}
    </button>
  );
}

export default function Layout({
  navbarContent,
  primarySidebarContent,
  mainContent,
  secondarySidebarContent,
  footerContent,
  primaryMinWidth = "48px",
  secondaryMinWidth = "48px",
}) {
  const hasPrimary = primarySidebarContent !== undefined;
  const hasSecondary = secondarySidebarContent !== undefined;

  const [primaryHidden, _setPrimaryHidden] = useState(() =>
    readStorage(KEY_PRIMARY, false)
  );
  const [secondaryHidden, _setSecondaryHidden] = useState(() =>
    readStorage(KEY_SECONDARY, false)
  );

  const setPrimaryHidden = useCallback((value) => {
    writeStorage(KEY_PRIMARY, value);
    _setPrimaryHidden(value);
  }, []);

  const setSecondaryHidden = useCallback((value) => {
    writeStorage(KEY_SECONDARY, value);
    _setSecondaryHidden(value);
  }, []);

  const togglePrimary = useCallback(() => {
    _setPrimaryHidden((prev) => {
      const next = !prev;
      writeStorage(KEY_PRIMARY, next);
      return next;
    });
  }, []);

  const toggleSecondary = useCallback(() => {
    _setSecondaryHidden((prev) => {
      const next = !prev;
      writeStorage(KEY_SECONDARY, next);
      return next;
    });
  }, []);

  const headerRef = useRef(null);
  const [sidebarVars, setSidebarVars] = useState({});

  const recalcDimensions = useCallback(() => {
    const h = headerRef.current?.offsetHeight ?? 0;
    setSidebarVars({
      "--sl-sidebar-height": `${window.innerHeight - h}px`,
      "--sl-sidebar-top": `${h}px`,
    });
  }, []);

  useEffect(() => {
    recalcDimensions();
    window.addEventListener("resize", recalcDimensions);
    return () => window.removeEventListener("resize", recalcDimensions);
  }, [recalcDimensions]);

  useEffect(() => {
    const offcanvasElements = document.querySelectorAll(".offcanvas.show");

    offcanvasElements.forEach((el) => {
      const instance = bootstrap.Offcanvas.getInstance(el);
      if (instance) {
        instance.hide();
      }
    });

    document.body.style.overflow = "";
    document.body.style.paddingRight = "";

  }, [location]);

  const ctx = {
    primaryHidden,
    secondaryHidden,
    togglePrimary,
    toggleSecondary,
    setPrimaryHidden,
    setSecondaryHidden,
  };

  const rootVars = {
    "--sl-primary-min-width": primaryMinWidth,
    "--sl-secondary-min-width": secondaryMinWidth,
  };
  
  return (
    <SidebarLayoutContext.Provider value={ctx}>
      <div style={rootVars}>

        {navbarContent !== undefined && (
          <header
            ref={headerRef}
            className="sl-header position-sticky top-0 bg-body"
          >
            <nav className="navbar">
              <div className="container-fluid gap-1 gap-sm-2 gap-md-3">

                {navbarContent}

                {hasPrimary && <PrimaryToggleButton className="d-none d-sm-block" />}
                {hasSecondary && <SecondaryToggleButton className="d-none d-xl-block" />}

                {hasPrimary && <PrimaryOffcanvasButton className="d-sm-none" />}
                {hasSecondary && <SecondaryOffcanvasButton className="d-xl-none" />}

              </div>
            </nav>
          </header>
        )}

        <div className="container-fluid sl-primary-layout p-0">
          {hasPrimary && (
            <div
              id="sl-primarySidebar"
              className={[
                "sl-sidebar sl-sidebar--primary",
                "offcanvas-sm offcanvas-start border-end border-secondary-subtle",
                primaryHidden ? "hide" : "",
              ].filter(Boolean).join(" ")}
              style={sidebarVars}
              tabIndex={-1}
            >
              <div className="offcanvas-header border-bottom d-none">
                <button
                  type="button"
                  className="btn-close shadow-none ms-auto"
                  data-bs-target="#sl-primarySidebar"
                  data-bs-dismiss="offcanvas"
                  aria-label="Tutup"
                />
              </div>
              <div className="offcanvas-body p-0 h-100">
                {primarySidebarContent}
              </div>
            </div>
          )}

          <div className="sl-main-content sl-secondary-layout">
            <main>
              {mainContent ?? <Outlet />}
            </main>

            {hasSecondary && (
              <aside
                id="sl-secondarySidebar"
                className={[
                  "sl-sidebar sl-sidebar--secondary",
                  "border-start offcanvas-end offcanvas-xl border-secondary-subtle",
                  secondaryHidden ? "hide" : "",
                ].filter(Boolean).join(" ")}
                style={sidebarVars}
                tabIndex={-1}
              >
                <div className="offcanvas-header border-bottom d-none">
                  <button
                    type="button"
                    className="btn-close shadow-none ms-auto"
                    data-bs-target="#sl-secondarySidebar"
                    data-bs-dismiss="offcanvas"
                    aria-label="Tutup"
                  />
                </div>
                <div className="offcanvas-body p-0 h-100">
                  {secondarySidebarContent}
                </div>
              </aside>
            )}
          </div>

        </div>

        {footerContent && (
          <footer className="bg-body border-top border-secondary-subtle p-3 py-5">
            <div className="container">
              <div className="row g-2">{footerContent}</div>
            </div>
          </footer>
        )}

      </div>
    </SidebarLayoutContext.Provider>
  );
}
```

- Salin kode berikut pada `src/layouts/Footer.jsx`
``jsx
export default function Footer() {
    return(
        <></>
    )
}
```

- Salin kode berikut pada `src/layouts/Brand.jsx`
```jsx
import { useContext } from "react"

export default function Brand() {
    return(
        <a href="#" className="navbar-brand link-dark text-decoration-none fs-5 font-serif text-primary fw-bold">
            Al-Quran
        </a>
    )
}
```

- Salin kode berikut pada `src/layouts/Header.jsx`
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

- Salin kode berikut pada `src/layouts/Nav.jsx`
```jsx
import { useId, useEffect, useRef, useState } from "react";
import { NavLink, useLocation } from "react-router-dom";
import { useSidebarLayout } from "./index.jsx";

function closeOffcanvas(event) {
    const offcanvasEl = event.currentTarget.closest(".offcanvas-start");

    if (!offcanvasEl) return;

    const instance = window.bootstrap.Offcanvas.getInstance(offcanvasEl);

    if (instance) {
        instance.hide();
    }
}

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
                    "text-decoration-none btn rounded-0 font-sans mb-1 w-100 text-start p-2 py-0 d-flex align-items-center",
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
                                onClick={closeOffcanvas}
                                className={({ isActive }) =>
                                    [baseClassName, isActive ? "btn-primary active text-nowrap" : "btn-light text-nowrap"]
                                        .join(" ")
                                }
                            >
                                {menu.label}
                            </NavLink>
                        ) : (
                            <a onClick={closeOffcanvas} {...commonProps} className={`${baseClassName} btn-light text-nowrap`} href={menu.href}>
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

- Update file `index.html`
```html
<!doctype html>
<html lang="en" data-bs-theme="light">
  <head>
    <title>Title</title>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />

    <link
      href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/css/bootstrap.min.css"
      rel="stylesheet"
      integrity="sha384-sRIl4kxILFvY47J16cr9ZwB07vP4J8+LH7qKQnuqkuIAvNWLzeN8tE5YBujZqJLB"
      crossorigin="anonymous"
    />
  </head>

  <body>
    <div id="root"></div>
    
    <script
      src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/js/bootstrap.bundle.min.js"
      integrity="sha384-FKyoEForCGlyvwx9Hj09JcYn3nv7wiPVlz7YYwJrWVcXK/BmnVDxM+D2scQbITxI"
      crossorigin="anonymous"
    ></script>

    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

- Update `src/App.jsx`
```jsx
import { useState } from 'react'
import Layout from './layouts'

function App() {
  return (
    <Layout
      primarySidebarContent={
        <p>Primary Sidebar Content</p>
      }
      mainContent={
        <p>Main Content</p>
      }
    />
  )
}

export default App
```

- Update `src/main.jsx`
```jsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import './styles/index.scss'
import App from './App.jsx'

createRoot(document.getElementById('root')).render(
  <StrictMode>
    <App />
  </StrictMode>,
)
```

- Update `src/styles/layout.scss`
```scss
$color-hover: #F5F4EE;
$color-active: #F3F2ED;
$primary-sidebar-bg: var(--bs-white);

$border-color: var(--bs-border-color-translucent);
$header-border-color: var(--bs-border-color);

$btn-icon-sm: 1.75rem;
$btn-icon-md: 2rem;
$btn-icon-lg: 2.5rem;

$sl-primary-width: 18rem;
$sl-secondary-width: 260px;
$sl-primary-min-width: 48px;
$sl-secondary-min-width: 48px;

:root {
    --sl-primary-width: #{$sl-primary-width};
    --sl-secondary-width: #{$sl-secondary-width};
    --sl-primary-min-width: #{$sl-primary-min-width};
    --sl-secondary-min-width: #{$sl-secondary-min-width};
}

.sl-sidebar {
    background-color: $primary-sidebar-bg !important;
    max-width: var(--sl-primary-width) !important;
    border-color: var(--bs-border-color) !important;
}

@media (min-width: 576px) {
    .sl-sidebar {
        transition: width 0.2s ease;
    }

    .sl-primary-layout {
        display: flex;

        >.sl-sidebar {
            width: var(--sl-primary-width);
            height: var(--sl-sidebar-height, 100vh);
            position: sticky;
            top: var(--sl-sidebar-top, 0);
            flex-shrink: 0;

            &.hide,
            &.hide .offcanvas-body {
                width: var(--sl-primary-min-width);

                .navbar-brand {
                    display: none;
                }
            }

            &.hide ::-webkit-scrollbar {
                width: 0;
                height: 0;
            }
        }

        >.sl-main-content {
            flex-grow: 1;
            min-width: 0;
        }
    }
}

@media (min-width: 1200px) {
    .sl-secondary-layout {
        display: flex;
        flex-direction: row;

        >.sl-sidebar {
            width: var(--sl-secondary-width);
            height: var(--sl-sidebar-height, 100vh);
            position: sticky;
            top: var(--sl-sidebar-top, 0);
            flex-shrink: 0;

            &.hide {
                width: var(--sl-secondary-min-width);
            }
        }

        >main {
            flex-grow: 1;
            min-width: 0;
        }
    }
}
```

- Update `src/layouts/index.scss`
```scss
@import url("https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/font/bootstrap-icons.min.css");

@import "./layout.scss";
```

## Mengatur Sidebar
- Update `src/Sidebar.jsx`
```jsx
import Brand from "./Brand"
import Nav from "./Nav";
import {
    PrimaryToggleButton
} from "../layouts/index.jsx";

const MENUS = [
    {
        to: "/",
        title: "Beranda",
        label: (
            <>
                <i className="bi bi-folder-symlink-fill opacity-50"></i>
                <span className="ms-2">Beranda</span>
            </>
        ),
    },
    {
        to: "/quran",
        title: "Al - Qur`an",
        label: (
            <>
                <i className="bi bi-folder-symlink-fill opacity-50"></i>
                <span className="ms-2">Al - Qur`an</span>
            </>
        ),
    },
    {
        to: "/tajwid",
        title: "Highlight Tajwid",
        label: (
            <>
                <i className="bi bi-folder-symlink-fill opacity-50"></i>
                <span className="ms-2">Highlight Tajwid</span>
            </>
        ),
    },
];

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
                            <Nav menus={MENUS} />
                        </div>
                    </div>
                </div>
            </div>
        </>
    )
}
```

- Update `src/App.jsx`
```jsx
import { Routes, Route } from "react-router-dom";

import Layout from "./layouts";
import Sidebar from "./layouts/Sidebar.jsx";


function App() {
    return (
        <Routes>
            <Route
                element={
                    <Layout
                        primarySidebarContent={<Sidebar />}
                    />
                }
            >
                <Route
                    path="/"
                    element={<p>Halaman Beranda</p>}
                />

                <Route
                    path="/quran"
                    element={<p>Halaman Al-Qur'an</p>}
                />

                <Route
                    path="/tajwid"
                    element={<p>Halaman Highlight Tajwid</p>}
                />
            </Route>
        </Routes>
    );
}

export default App;
```
- Update `src/main.jsx`
```jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import { BrowserRouter } from "react-router-dom";

import "./styles/index.scss";
import App from "./App.jsx";

import AOS from "aos";
import "aos/dist/aos.css";

AOS.init({
    duration: 800,
    once: true,
});

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </StrictMode>
);
```

## Mackover Sidebar
- Update `src/styles/sidebar.scss`
```scss
$sidebar-bg: $sidebar-bg;
$sidebar-border: rgba(255, 255, 255, 0.06);

$text-muted: #8a8a8a;
$text-light: #f5f5f5;
$accent: $primary-color;

$item-radius: .5rem;
$item-padding-y: .5rem;
$item-padding-x: .75rem;
$item-gap: .25rem;

$hover-bg: rgba(255, 255, 255, 0.05);
$active-bg: rgba(255, 255, 255, 0.045);

$font-sans: $font-primary;

#sl-primarySidebar.sl-sidebar--primary {
  background-color: $sidebar-bg !important;
  border: 1px solid $sidebar-border !important;
  padding: 1rem .85rem !important;
  overflow: hidden;

  &.border-end,
  &.border-secondary-subtle {
    border-color: $sidebar-border !important;
  }

  .offcanvas-header {
    display: none !important;
  }

  .offcanvas-body {
    padding: 0 !important;
    height: 100% !important;
    background: transparent !important;
  }

  .container,
  .row,
  .col-12 {
    width: 100% !important;
    max-width: none !important;
    padding: 0 !important;
    margin: 0 !important;
  }

  .row {
    row-gap: $item-gap !important;
  }

  .nav-menu>.navbar-brand {
    display: flex !important;
    align-items: center;
    gap: .6rem;
    width: 100%;
    padding: .5rem .5rem .9rem !important;
    margin: 0 0 .25rem !important;
    font-weight: 700 !important;
    font-size: 1.05rem !important;
    letter-spacing: .04em;
    text-transform: uppercase;
    color: $text-light !important;

    &::before {
      content: "";
      flex: 0 0 auto;
      width: 18px;
      height: 18px;
      border-radius: 50%;
      border: 3px solid $accent;
      background: transparent;
      box-sizing: border-box;
    }

    &:hover {
      color: $text-light !important;
    }
  }

  .accordion {
    width: 100%;
    display: flex;
    flex-direction: column;
    gap: $item-gap;
    background: transparent !important;
    border: 0 !important;
  }

  .accordion-item {
    background: transparent !important;
    border: 0 !important;
    border-radius: 0 !important;
    width: 100%;
  }

  .accordion-item>a.btn {
    display: flex !important;
    align-items: center;
    width: 100%;
    padding: $item-padding-y $item-padding-x !important;
    border-radius: $item-radius !important;
    border: 0 !important;
    background-color: transparent !important;
    color: $text-muted !important;
    font-family: $font-sans !important;
    font-size: .9rem !important;
    font-weight: 500 !important;
    line-height: 1.2;
    white-space: nowrap;
    transition: background-color .15s ease, color .15s ease;

    i {
      font-size: 1rem;
      width: 20px;
      text-align: center;
      color: inherit !important;
    }

    span {
      color: inherit !important;
      font-weight: inherit !important;
    }

    &:hover,
    &:focus {
      background-color: $hover-bg !important;
      color: $text-light !important;
      box-shadow: none !important;
    }

    &:active {
      background-color: $hover-bg !important;
    }
  }

  .accordion-item>a.btn.btn-primary.active,
  .accordion-item>a.btn.btn-primary.active:hover,
  .accordion-item>a.btn.btn-primary.active:focus {
    position: relative;
    background-color: $active-bg !important;
    color: $accent !important;
    font-weight: 600 !important;

    i {
      color: $accent !important;
    }

    &::before {
      content: "";
      position: absolute;
      left: 0;
      top: 50%;
      transform: translateY(-50%);
      width: 3px;
      height: 55%;
      border-radius: 3px;
      background-color: $accent;
    }
  }
}
```

- Update `src/styles/variables.scss`
```scss
// Cinzel
@import url('https://fonts.googleapis.com/css2?family=Cinzel+Decorative:wght@400;700;900&display=swap');

$sidebar-bg: #161616;

$primary-color: #d3ff4d;

$font-primary: "Cinzel Decorative", serif;
```

- Update `src/styles/index.scss`
```scss
@import url("https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/font/bootstrap-icons.min.css");

@import "./variables.scss";
@import "./layout.scss";
@import "./sidebar.scss";
@import "./brand.scss";
@import "./card.scss";
```

## Membuat layout responsive
- Update `src/App.jsx`
```jsx
import { Routes, Route } from "react-router-dom";

import Layout from "./layouts";
import Sidebar from "./layouts/Sidebar.jsx";
import {
 PrimaryOffcanvasButton
} from "./layouts/index.jsx";
import Brand from "./layouts/Brand.jsx";

function App() {
  return (
    <Routes>
      <Route
        element={
          <>
            <header className="py-2 mb-4 position-sticky top-0 bg-body d-sm-none z-3">
              <div className="container">
                <div className="row">
                  <div className="col-12 d-flex align-items-center justify-content-between">
                    <Brand />
                    <PrimaryOffcanvasButton className="btn-dark rounded-pill text-nowrap text-uppercase">
                      <i className="bi bi-list me-2"></i>
                      Menu
                    </PrimaryOffcanvasButton>
                  </div>
                </div>
              </div>
            </header>
            <Layout
              primarySidebarContent={<Sidebar />}
            />
          </>
        }
      >
        <Route
          path="/"
          element={<p>Halaman Beranda</p>}
        />

        <Route
          path="/quran"
          element={<p>Halaman Al-Qur'an</p>}
        />

        <Route
          path="/tajwid"
          element={<p>Halaman Highlight Tajwid</p>}
        />
      </Route>
    </Routes>
  );
}

export default App;
```

- Update `src/styles/brand.scss`
```scss
.navbar-brand {
  display: flex !important;
  align-items: center;
  gap: .6rem;
  width: 100%;
  font-family: $font-sans !important;
  font-weight: 700 !important;
  font-size: 1.05rem !important;
  letter-spacing: .04em;
  text-transform: uppercase;
  color: var(--bs-body-color) !important;

  &::before {
    content: "";
    flex: 0 0 auto;
    width: 18px;
    height: 18px;
    border-radius: 50%;
    border: 3px solid $accent;
    background: transparent;
    box-sizing: border-box;
  }
}
```

- Update `app/styles/index.scss```

```scss
@import url("https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/font/bootstrap-icons.min.css");

@import "./layout.scss";
@import "./sidebar.scss";
@import "./brand.scss";
```

## Membuat halaman beranda
- Update `src/pages/home.jsx`
```jsx
import { NavLink } from "react-router-dom";

export default function Home() {
    return (
        <>
            <section className="py-5 home">
                <div className="container">
                    <div className="row justify-content-center">
                        <div className="col-11">
                            <h1 className="display-1" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">
                                Deteksi <span className="bg-dark text-white text-nowrap px-3 rounded-3">Tajwid</span>
                                Secara Otomatis
                            </h1>

                            <p className="fs-4" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">
                                Teknologi yang membantu mengenali hukum tajwid melalui penandaan warna sehingga membaca Al-Qur'an menjadi lebih mudah dipahami.
                            </p>

                            <div className="w-100 d-flex gap-1 flex-wrap">
                                <NavLink
                                    to="/tajwid"
                                    className="btn btn-lg btn-dark"
                                    data-aos="fade-up" data-aos-anchor-placement="bottom-bottom"
                                >
                                    Coba Sekarang
                                </NavLink>
                                <NavLink
                                    to="/quran"
                                    className="btn btn-lg btn-primary"
                                    data-aos="fade-up" data-aos-anchor-placement="bottom-bottom"
                                >
                                    Quran
                                </NavLink>
                            </div>
                        </div>
                    </div>
                </div>
            </section>
            <section className="py-5 border-top home overflow-hidden">
                <div className="container">
                    <div className="row justify-content-center">
                        <div className="col-11 col-sm-10 text-center">
                            <h1 className="display-2" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">
                                Hukum <span className="bg-dark text-white text-nowrap px-3 rounded-3">Tajwid</span>
                            </h1>
                            <p className="fs-4" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">
                                beberapa hukum tajwid yang saat ini didukung oleh fitur deteksi dan <em>highlight</em> pada aplikasi.
                            </p>
                        </div>
                    </div>
                    <div className="row justify-content-center mt-5 g-5">

                        {/* Izhar Halqi */}
                        <div className="col-11 col-md-6">
                            <div
                                className="card border border-secondary-subtle rounded-0 h-100 shadow-sm"
                                style={{ borderLeft: "8px solid rgb(179, 74, 0)" }} 
                                data-aos="fade-up" data-aos-anchor-placement="bottom-bottom"
                            >
                                <div className="corner top left"></div>
                                <div className="corner top right"></div>
                                <div className="corner bottom left"></div>
                                <div className="corner bottom right"></div>

                                <div className="card-body">
                                    <h3
                                        className="mb-3"
                                        style={{ color: "rgb(179, 74, 0)" }}
                                    >
                                        Izhar Halqi
                                    </h3>

                                    <p className="mb-0">
                                        Apabila nun sukun <span className="font-lpmq fs-5">نْ</span>
                                        atau tanwin (<span className="font-lpmq fs-5">ً ٍ ٌ</span>)
                                        bertemu dengan huruf
                                        <span className="font-lpmq fs-5"> ء هـ ع ح غ خ</span>,
                                        maka dibaca jelas tanpa dengung.
                                    </p>
                                </div>
                            </div>
                        </div>

                        {/* Idgham Bighunnah */}
                        <div className="col-11 col-md-6">
                            <div
                                className="card border border-secondary-subtle rounded-0 h-100 shadow-sm"
                                style={{ borderLeft: "8px solid rgb(0, 128, 0)" }}
                                data-aos="fade-up" data-aos-anchor-placement="bottom-bottom"
                            >
                                <div className="corner top left"></div>
                                <div className="corner top right"></div>
                                <div className="corner bottom left"></div>
                                <div className="corner bottom right"></div>

                                <div className="card-body">
                                    <h3
                                        className="mb-3"
                                        style={{ color: "rgb(0, 128, 0)" }}
                                    >
                                        Idgham Bighunnah
                                    </h3>

                                    <p className="mb-0">
                                        Apabila nun sukun (<span className="font-lpmq fs-5">نْ</span>)
                                        atau tanwin (<span className="font-lpmq fs-5">ً ٍ ٌ</span>)
                                        bertemu dengan huruf
                                        <span className="font-lpmq fs-5"> ي ن م و</span>,
                                        maka dibaca melebur disertai dengung selama 2–3 harakat.
                                    </p>
                                </div>
                            </div>
                        </div>

                        {/* Ikhfa Haqiqi */}
                        <div className="col-11 col-md-6">
                            <div
                                className="card border border-secondary-subtle rounded-0 h-100 shadow-sm"
                                style={{ borderLeft: "8px solid rgb(189, 15, 30)" }}
                                data-aos="fade-up" data-aos-anchor-placement="bottom-bottom"
                            >
                                <div className="corner top left"></div>
                                <div className="corner top right"></div>
                                <div className="corner bottom left"></div>
                                <div className="corner bottom right"></div>

                                <div className="card-body">
                                    <h3
                                        className="mb-3"
                                        style={{ color: "rgb(189, 15, 30)" }}
                                    >
                                        Ikhfa Haqiqi
                                    </h3>

                                    <p className="mb-0">
                                        Apabila nun sukun (<span className="font-lpmq fs-5">نْ</span>)
                                        atau tanwin (<span className="font-lpmq fs-5">ً ٍ ٌ</span>)
                                        bertemu dengan huruf
                                        <span className="font-lpmq fs-5">
                                            {" "}
                                            ت ث ج د ذ ز س ش ص ض ط ظ ف ق ك
                                        </span>
                                        , maka dibaca samar dengan dengung selama 2–3 harakat.
                                    </p>
                                </div>
                            </div>
                        </div>

                        {/* Iqlab */}
                        <div className="col-11 col-md-6">
                            <div
                                className="card border border-secondary-subtle rounded-0 h-100 shadow-sm"
                                style={{ borderLeft: "8px solid rgb(0, 128, 128)" }}
                                data-aos="fade-up" data-aos-anchor-placement="bottom-bottom"
                            >
                                <div className="corner top left"></div>
                                <div className="corner top right"></div>
                                <div className="corner bottom left"></div>
                                <div className="corner bottom right"></div>

                                <div className="card-body">
                                    <h3
                                        className="mb-3"
                                        style={{ color: "rgb(0, 128, 128)" }}
                                    >
                                        Iqlab
                                    </h3>

                                    <p className="mb-0">
                                        Apabila nun sukun (<span className="font-lpmq fs-5">نْ</span>)
                                        atau tanwin (<span className="font-lpmq fs-5">ً ٍ ٌ</span>)
                                        bertemu huruf <span className="font-lpmq fs-5">ب</span>,
                                        maka bunyi nun berubah menjadi mim (
                                        <span className="font-lpmq fs-5">م</span>) dan dibaca
                                        berdengung selama 2–3 harakat.
                                    </p>
                                </div>
                            </div>
                        </div>

                        {/* Mad Thabi'i */}
                        <div className="col-11 col-md-12">
                            <div
                                className="card border border-secondary-subtle rounded-0 h-100 shadow-sm"
                                style={{ borderLeft: "8px solid rgb(129, 0, 194)" }}
                                data-aos="fade-up" data-aos-anchor-placement="bottom-bottom"
                            >
                                <div className="corner top left"></div>
                                <div className="corner top right"></div>
                                <div className="corner bottom left"></div>
                                <div className="corner bottom right"></div>

                                <div className="card-body">
                                    <h3
                                        className="mb-3"
                                        style={{ color: "rgb(129, 0, 194)" }}
                                    >
                                        Mad Thabi'i
                                    </h3>

                                    <p className="mb-0">
                                        Mad asli yang terdapat dalam satu kata dan dibaca
                                        sepanjang <strong>2 harakat (1 alif)</strong>.
                                    </p>
                                </div>
                            </div>
                        </div>

                    </div>
                </div>
            </section>
        </>
    )
}
```

- Update `src/styles/home.scss`
```scss
.home {
    font-family: $font-tertiary;

    .container {
        max-width: 46rem;
    }

    h1,
    h3,
    .btn {
        font-family: $font-secondary;
    }

    &:nth-child(1) {
        background-image: linear-gradient(90deg, rgb(240, 240, 240) 1px, rgba(0, 0, 0, 0) 1px), linear-gradient(rgb(240, 240, 240) 1px, rgba(0, 0, 0, 0) 1px);
        background-size: 4rem 4rem;
    }
}
```

- Update `src/App.jsx`
```jsx
import { Routes, Route } from "react-router-dom";

import Layout from "./layouts";
import Sidebar from "./layouts/Sidebar.jsx";
import {
  PrimaryOffcanvasButton
} from "./layouts/index.jsx";
import Brand from "./layouts/Brand.jsx";
import Home from "./pages/Home.jsx";

function App() {
  return (
    <Routes>
      <Route
        element={
          <>
            <header className="py-2 mb-4 position-sticky top-0 bg-body d-sm-none z-3">
              <div className="container">
                <div className="row">
                  <div className="col-12 d-flex align-items-center justify-content-between">
                    <Brand />
                    <PrimaryOffcanvasButton className="btn-dark rounded-pill text-nowrap text-uppercase">
                      <i className="bi bi-list me-2"></i>
                      Menu
                    </PrimaryOffcanvasButton>
                  </div>
                </div>
              </div>
            </header>
            <Layout
              primarySidebarContent={<Sidebar />}
            />
          </>
        }
      >
        <Route
          path="/"
          element={
            <>
              <Home />
            </>
          }
        />

        <Route
          path="/quran"
          element={<p>Halaman Al-Qur'an</p>}
        />

        <Route
          path="/tajwid"
          element={<p>Halaman Highlight Tajwid</p>}
        />
      </Route>
    </Routes>
  );
}

export default App;
```

- Update `src/styles/utils.scss`
```scss
@font-face {
    font-family: "Anthropic Sans";
    src: url("../assets/fonts/Anthropic_Sans_Text.woff2") format("woff2");
    font-weight: 100 900;
    font-style: normal;
    font-display: swap;
}

@font-face {
    font-family: "Anthropic Mono";
    src: url("../assets/fonts/Anthropic Mono.woff2") format("woff2");
    font-weight: 100 900;
    font-style: normal;
    font-display: swap;
}

@font-face {
    font-family: "Anthropic Serif";
    src: url("../assets/fonts/Anthropic_Serif_Text_Light.woff2") format("woff2");
    font-weight: 300;
    font-style: normal;
    font-display: swap;
}

@font-face {
    font-family: "LPMQ";
    src: url("../assets/fonts/LPMQ.ttf") format("truetype");
    font-weight: normal;
    font-style: normal;
    font-display: swap;
}

@font-face {
    font-family: "Thuluth";
    src: url("../assets/fonts/thuluth.ttf") format("truetype");
    font-weight: normal;
    font-style: normal;
    font-display: swap;
}

.font-sans {
    font-family: "Anthropic Sans", sans-serif !important;
}

.font-serif {
    font-family: "Anthropic Serif", serif !important;
}

.font-mono {
    font-family: "Anthropic Mono", monospace !important;
}

.font-lpmq {
    font-family: "LPMQ", serif !important;
    font-weight: normal;
    unicode-bidi: isolate;
    font-variant-ligatures: common-ligatures contextual;
    font-feature-settings: "liga", "calt", "rlig";
}

.font-thuluth {
    font-family: "Thuluth", serif !important;
    font-weight: normal;
    unicode-bidi: isolate;
    font-variant-ligatures: common-ligatures contextual;
    font-feature-settings: "liga", "calt", "rlig";
}

.font-arabic {
    direction: rtl;
    line-height: 2;
    unicode-bidi: isolate;
    font-variant-ligatures: common-ligatures contextual;
    font-feature-settings: "liga", "calt", "rlig";
}

.font-lpmq-lg {
    font-family: "LPMQ", serif !important;
    font-size: 2rem;
    line-height: 2;
    direction: rtl;
    font-variant-ligatures: common-ligatures contextual;
    font-feature-settings: "liga", "calt", "rlig";

}

.font-thuluth-lg {
    font-family: "Thuluth", serif !important;
    font-size: 2.2rem;
    line-height: 2;
    direction: rtl;
    unicode-bidi: isolate;
    font-variant-ligatures: common-ligatures contextual;
    font-feature-settings: "liga", "calt", "rlig";
}

:root {
    --primary: #d3ff4d;
    --primary-hover: #c2ef37;
    --primary-active: #aed72a;
    --primary-disabled: #e7ff9a;
}

.btn-primary {
    color: #212529 !important;
    background-color: var(--primary) !important;
    border-color: var(--primary) !important;
    font-weight: 600;
}

.btn-primary:hover,
.btn-primary.hover {
    color: #212529 !important;
    background-color: var(--primary-hover) !important;
    border-color: var(--primary-hover) !important;
}

.btn-primary:focus,
.btn-primary:focus-visible {
    color: #212529 !important;
    background-color: var(--primary-hover) !important;
    border-color: var(--primary-hover) !important;
    box-shadow: 0 0 0 .25rem rgba(211, 255, 77, .35) !important;
}

.btn-primary:active,
.btn-primary.active,
.show > .btn-primary.dropdown-toggle {
    color: #212529 !important;
    background-color: var(--primary-active) !important;
    border-color: var(--primary-active) !important;
}

.btn-primary:disabled,
.btn-primary.disabled {
    color: #212529 !important;
    background-color: var(--primary-disabled) !important;
    border-color: var(--primary-disabled) !important;
    opacity: .65;
}
```

- Update `src\styles\variables.scss`
```scss
@import url('https://fonts.googleapis.com/css2?family=Cinzel+Decorative:wght@400;700;900&display=swap');
@import url('https://fonts.googleapis.com/css2?family=IM+Fell+English+SC&family=Sofia+Sans:ital,wght@0,1..1000;1,1..1000&display=swap');

$sidebar-bg: #161616;

$primary-color: #d3ff4d;

$font-primary: "Cinzel Decorative", serif;
$font-secondary: "IM Fell English SC", serif;
$font-tertiary: "Sofia Sans", sans-serif;
```
- Update `src/styles/index.scss`
```scss
@import url("https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/font/bootstrap-icons.min.css");

@import "./variables.scss";
@import "./layout.scss";
@import "./sidebar.scss";
@import "./brand.scss";
@import "./card.scss";
@import "./home.scss";
@import "./utils.scss";
```

## Membuat halaman daftar surah
- Update `src/services/api/js`
```js
import axios from "axios";

const api = axios.create({
    baseURL: "https://equran.id/api/v2",
    timeout: 10000,
    headers: {
        "Content-Type": "application/json",
    },
});

export default api;
```

- Update `src/services/surah.services.js`
```js
import api from "./api";

export const SurahService = {

    /**
     * Mengambil seluruh daftar surah.
     */
    async getAll() {
        const response = await api.get("/surat");
        return response.data.data;
    },

    /**
     * Mengambil detail satu surah.
     */
    async getByNumber(number) {
        const response = await api.get(`/surat/${number}`);
        return response.data.data;
    },

    async getById(nomor) {
        const { data } = await api.get(`/surat/${nomor}`);
        return data.data;
    }

};
```

- Update `src/components/Input.jsx`
```jsx
import { useState, forwardRef } from "react";

const Input = forwardRef(({
    label,
    className = "",
    type = "text",
    placeholder = "",
    value,
    defaultValue = "",
    onChange,
    id,
    ...props
}, ref) => {
    const isControlled = value !== undefined;
    const [internalValue, setInternalValue] = useState(defaultValue);
    const inputValue = isControlled ? value : internalValue;

    const handleChange = (e) => {
        if (!isControlled) setInternalValue(e.target.value);
        onChange?.(e);
    };

    return (
        <div className={`form-floating ${className || "mb-3"}`}>
            <input
                ref={ref}
                {...props}
                type={type}
                onChange={handleChange}
                value={inputValue}
                className="form-control rounded-4 px-3"
                id={id}
                required
                placeholder={placeholder}
            />
            <label className="ms-1" htmlFor={id}>{label}</label>
        </div>
    );
});

Input.displayName = "Input";
export default Input;
```

- Update `src/styles/index.scss`
```scss
@import url("https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/font/bootstrap-icons.min.css");

@import "./variables.scss";
@import "./layout.scss";
@import "./sidebar.scss";
@import "./brand.scss";
@import "./card.scss";
@import "./home.scss";
@import "./surah.scss";
@import "./utils.scss";
```

- Update `src/pages/Surah.jsx`
```jsx
import { useState, useEffect, useMemo } from "react";
import { SurahService } from "../services/surah.services";
import Input from "../components/Input";
import { NavLink } from "react-router-dom";

export default function Surah() {

    const [surahs, setSurahs] = useState([]);
    const [keyword, setKeyword] = useState("");

    useEffect(() => {

        async function load() {
            try {
                const data = await SurahService.getAll();
                setSurahs(data);
            } catch (err) {
                console.error(err);
            }
        }

        load();

    }, []);

    const filteredSurahs = useMemo(() => {

        const q = keyword.toLowerCase().trim();

        if (!q) return surahs;

        return surahs.filter((surah) =>
            surah.namaLatin.toLowerCase().includes(q) ||
            surah.arti.toLowerCase().includes(q) ||
            surah.nomor.toString().includes(q)
        );

    }, [keyword, surahs]);

    return (
        <>
            <section className="surah py-5">
                <div className="container">
                    <div className="row justify-content-center">
                        <div className="col-11 col-sm-10 text-center">
                            <h1 className="display-2" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">
                                Daftar <span className="bg-dark text-white text-nowrap px-3 rounded-3">Surah</span>
                            </h1>

                            <p className="fs-4" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">
                                Pilih salah satu surah Al-Qur'an untuk mulai membaca dengan fitur
                                deteksi dan <em>highlight</em> hukum tajwid.
                            </p>
                        </div>
                    </div>
                </div>
            </section>
            <section className="py-3 position-sticky top-0 z-3 bg-body surah">
                <div className="container">
                    <div className="row justify-content-center">
                        <div className="col-11 col-md-12" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">
                            <Input
                                id="search-surah"
                                label="Cari Surah"
                                placeholder="Nama surah, arti, atau nomor"
                                value={keyword}
                                onChange={(e) => setKeyword(e.target.value)}
                            />
                        </div>
                    </div>
                </div>
            </section>
            <section class="surah pt-3 pb-5 overflow-hidden">
                <div className="container">
                    <div className="row g-5 justify-content-center">
                        {filteredSurahs.map((surah) => (
                            <div className="col-11 col-md-12 px-4" key={surah.nomor}>
                                <div
                                    data-aos="fade-up" data-aos-anchor-placement="bottom-bottom"
                                    className="card border border-secondary-subtle rounded-0 h-100 shadow-sm mx-2"
                                >
                                    <div className="corner top left"></div>
                                    <div className="corner top right"></div>
                                    <div className="corner bottom left"></div>
                                    <div className="corner bottom right"></div>

                                    <div className="card-body">
                                        <h3
                                            className="mb-3"
                                        >
                                            <span className="bg-dark px-2 text-white me-1 fs-4">{surah.nomor}</span>
                                            {surah.namaLatin}
                                        </h3>

                                        <p tabIndex={0} className="description" dangerouslySetInnerHTML={{ __html: surah.deskripsi }} />
                                        <p className="mb-0 text-muted">
                                            <span className="badge border rounded-0 text-body">{surah.jumlahAyat} Ayat</span>
                                            <span className="badge border rounded-0 text-body ms-1">Turun di {surah.tempatTurun}</span>
                                        </p>
                                    </div>
                                    <div className="card-footer bg-transparent border-top-0 pb-3">
                                        <NavLink
                                            to={`/surahs/${surah.nomor}`}
                                            className="btn btn-primary ms-2 rounded-0"
                                        >
                                            Baca Sekarang
                                        </NavLink>
                                    </div>
                                </div>
                            </div>
                        ))}
                    </div>
                </div>
            </section>
        </>
    )
}
```

- Update `src/styles/surah.scss`
```scss
.surah {
    font-family: $font-tertiary;

    .container {
        max-width: 46rem;
    }

    h1,
    h3,
    .btn {
        font-family: $font-secondary;
    }

    &:nth-child(1) {
        background-image: linear-gradient(90deg, rgb(240, 240, 240) 1px, rgba(0, 0, 0, 0) 1px), linear-gradient(rgb(240, 240, 240) 1px, rgba(0, 0, 0, 0) 1px);
        background-size: 4rem 4rem;
    }

    .form-control:focus {
        color: var(--bs-body-color);
        background-color: var(--bs-body-bg);

        border-color: #d3ff4d;
        outline: 0;

        box-shadow: 0 0 0 .25rem rgba(211, 255, 77, .25);
    }

    .description {
        display: -webkit-box;
        -webkit-line-clamp: 3;
        -webkit-box-orient: vertical;
        overflow: hidden;
        cursor: pointer;
        position: relative;
    }

    .description:focus {
        -webkit-line-clamp: unset;
    }
}
```
- Update `src/App.jsx`
```jsx
import { Routes, Route } from "react-router-dom";

import Layout from "./layouts";
import Sidebar from "./layouts/Sidebar.jsx";
import {
  PrimaryOffcanvasButton
} from "./layouts/index.jsx";
import Brand from "./layouts/Brand.jsx";
import Home from "./pages/Home.jsx";
import Surah from "./pages/Surah.jsx";

function App() {
  return (
    <Routes>
      <Route
        element={
          <>
            <header className="py-2 mb-4 position-sticky top-0 bg-body d-sm-none z-3">
              <div className="container">
                <div className="row">
                  <div className="col-12 d-flex align-items-center justify-content-between">
                    <Brand />
                    <PrimaryOffcanvasButton className="btn-dark rounded-pill text-nowrap text-uppercase">
                      <i className="bi bi-list me-2"></i>
                      Menu
                    </PrimaryOffcanvasButton>
                  </div>
                </div>
              </div>
            </header>
            <Layout
              primarySidebarContent={<Sidebar />}
            />
          </>
        }
      >
        <Route
          path="/"
          element={
            <>
              <Home />
            </>
          }
        />

        <Route
          path="/quran"
          element={<Surah/>}
        />

        <Route
          path="/tajwid"
          element={<p>Halaman Highlight Tajwid</p>}
        />
      </Route>
    </Routes>
  );
}

export default App;
```

## Membuat halaman surah tertentu dengan ayatnya
- Update `src/pages/Ayah.jsx`
```jsx
import { useEffect, useRef, useState } from "react";
import { NavLink, useParams } from "react-router-dom";
import { SurahService } from "../services/surah.services";
import Tajwid from "../hooks/Tajwid";

export default function Ayah() {
    const { nomor } = useParams();

    const [surah, setSurah] = useState(null);
    const [loading, setLoading] = useState(true);
    const [copiedAyat, setCopiedAyat] = useState(null);

    const audioRef = useRef(new Audio());

    // index ayat yang sedang diputar
    const [currentIndex, setCurrentIndex] = useState(-1);

    // ref setiap ayat
    const ayatRefs = useRef({});

    useEffect(() => {
        async function load() {
            try {
                setLoading(true);

                const data = await SurahService.getById(nomor);

                setSurah(data);
                setCurrentIndex(-1);

                audioRef.current.pause();
                audioRef.current.src = "";
            } catch (err) {
                console.error(err);
            } finally {
                setLoading(false);
            }
        }

        load();

        return () => {
            audioRef.current.pause();
            audioRef.current.src = "";
        };
    }, [nomor]);

    useEffect(() => {
        const audio = audioRef.current;

        const handleEnded = () => {
            const nextIndex = currentIndex + 1;

            if (surah && nextIndex < surah.ayat.length) {
                playIndex(nextIndex);
            } else {
                setCurrentIndex(-1);
            }
        };

        audio.addEventListener("ended", handleEnded);

        return () => {
            audio.removeEventListener("ended", handleEnded);
        };
    }, [currentIndex, surah]);

    async function copyArabicText(ayat) {
        try {
            await navigator.clipboard.writeText(ayat.teksArab);

            setCopiedAyat(ayat.nomorAyat);

            setTimeout(() => {
                setCopiedAyat((prev) =>
                    prev === ayat.nomorAyat ? null : prev
                );
            }, 2000);
        } catch (err) {
            console.error(err);
            alert("Gagal menyalin teks.");
        }
    }

    async function playIndex(index) {
        if (!surah) return;

        const ayat = surah.ayat[index];

        if (!ayat) {
            setCurrentIndex(-1);
            return;
        }

        const audio = audioRef.current;

        audio.pause();

        audio.src = ayat.audio["02"];
        audio.currentTime = 0;

        try {
            await audio.play();

            setCurrentIndex(index);

            ayatRefs.current[ayat.nomorAyat]?.scrollIntoView({
                behavior: "smooth",
                block: "center"
            });
        } catch (err) {
            console.error(err);
        }
    }

    function playAyat(index) {
        if (currentIndex === index) {
            audioRef.current.pause();
            setCurrentIndex(-1);
            return;
        }

        playIndex(index);
    }

    if (loading) {
        return (
            <section className="ayah py-5">
                <div className="container">
                    <div className="row justify-content-center">
                        <div className="col-11 text-center">

                            <h1 className="display-3">
                                Memuat Surah
                            </h1>

                        </div>
                    </div>
                </div>
            </section>
        );
    }

    if (!surah) {
        return (
            <section className="ayah py-5">
                <div className="container">
                    <div className="row justify-content-center">
                        <div className="col-11 text-center">

                            <h1 className="display-3">
                                Surah tidak ditemukan
                            </h1>
                            <NavLink
                                to="/quran"
                                className="btn btn-primary rounded-0 mt-3"
                            >
                                Kembali
                            </NavLink>

                        </div>
                    </div>
                </div>
            </section>
        );
    }

    return (
        <>
            <section className="ayah py-5">
                <div className="container">
                    <div className="row justify-content-center">
                        <div className="col-11 text-center">

                            <h1 className="display-3" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">
                                {surah.namaLatin}
                            </h1>

                            <p className="mb-0" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">
                                <span className="badge border rounded-0 text-body">
                                    {surah.arti}
                                </span>

                                <span className="badge border rounded-0 text-body ms-2">
                                    {surah.jumlahAyat} Ayat
                                </span>

                                <span className="badge border rounded-0 text-body ms-2">
                                    {surah.tempatTurun}
                                </span>
                            </p>

                        </div>
                    </div>
                </div>
            </section>

            <section className="pb-5 surah">
                <div className="container">

                    <div className="row justify-content-center g-4">

                        {surah.ayat.map((ayat, index) => (

                            <div
                                key={ayat.nomorAyat}
                                className="col-12"
                                ref={(el) => {
                                    ayatRefs.current[ayat.nomorAyat] = el;
                                }}
                            >

                                <div
                                    data-aos="fade-up" data-aos-anchor-placement="bottom-bottom"
                                    className={`card border rounded-0 h-100 mx-2 ${currentIndex === index
                                        ? "border-success shadow"
                                        : "border-secondary-subtle shadow-sm"
                                        }`}
                                >

                                    <div className="corner top left"></div>
                                    <div className="corner top right"></div>
                                    <div className="corner bottom left"></div>
                                    <div className="corner bottom right"></div>

                                    <div className="card-header d-flex align-items-center gap-2 bg-light-subtle">
                                        <span className="badge bg-dark rounded-0 fs-4">
                                            {ayat.nomorAyat}
                                        </span>

                                        <button
                                            className={`btn rounded-0 h-100 d-flex align-items-center display-6 ${currentIndex === index
                                                ? "btn-danger"
                                                : "btn-primary"
                                                }`}
                                            onClick={() => playAyat(index)}
                                        >
                                            <i
                                                className={`bi ${currentIndex === index
                                                    ? "bi-stop-fill"
                                                    : "bi-play-fill"
                                                    }`}
                                            />
                                        </button>
                                        <button
                                            className={`btn rounded-0 h-100 d-flex align-items-center display-6 ms-auto btn-dark`}
                                            onClick={() => copyArabicText(ayat)}
                                            title="Salin teks Arab"
                                        >
                                            <i
                                                className={`bi me-2 ${copiedAyat === ayat.nomorAyat
                                                        ? "bi-check-lg"
                                                        : "bi-copy"
                                                    }`}
                                            />
                                            Salin Teks Arab
                                        </button>
                                    </div>
                                    <div className="card-body">

                                        <p
                                            dir="rtl"
                                            className="fs-2 text-end font-lpmq"
                                        >
                                            <Tajwid text={ayat.teksArab} />
                                        </p>

                                        <p className="fw-semibold">
                                            {ayat.teksLatin}
                                        </p>

                                        <p className="mb-0">
                                            {ayat.teksIndonesia}
                                        </p>

                                    </div>

                                </div>

                            </div>

                        ))}

                    </div>

                </div>
            </section>
        </>
    );
}
```

- Update `src/styles/ayah.scss`
```scss
.ayah {
    font-family: $font-tertiary;

    .container {
        max-width: 46rem;
    }

    h1,
    h3,
    .btn {
        font-family: $font-secondary;
    }

    &:nth-child(1) {
        background-image: linear-gradient(90deg, rgb(240, 240, 240) 1px, rgba(0, 0, 0, 0) 1px), linear-gradient(rgb(240, 240, 240) 1px, rgba(0, 0, 0, 0) 1px);
        background-size: 4rem 4rem;
    }
}
```

- Update `src/styles/utils.scss`
```scss
@font-face {
    font-family: "Anthropic Sans";
    src: url("../assets/fonts/Anthropic_Sans_Text.woff2") format("woff2");
    font-weight: 100 900;
    font-style: normal;
    font-display: swap;
}

@font-face {
    font-family: "Anthropic Mono";
    src: url("../assets/fonts/Anthropic Mono.woff2") format("woff2");
    font-weight: 100 900;
    font-style: normal;
    font-display: swap;
}

@font-face {
    font-family: "Anthropic Serif";
    src: url("../assets/fonts/Anthropic_Serif_Text_Light.woff2") format("woff2");
    font-weight: 300;
    font-style: normal;
    font-display: swap;
}

@font-face {
    font-family: "LPMQ";
    src: url("../assets/fonts/LPMQ.ttf") format("truetype");
    font-weight: normal;
    font-style: normal;
    font-display: swap;
}

@font-face {
    font-family: "Thuluth";
    src: url("../assets/fonts/thuluth.ttf") format("truetype");
    font-weight: normal;
    font-style: normal;
    font-display: swap;
}

.font-sans {
    font-family: "Anthropic Sans", sans-serif !important;
}

.font-serif {
    font-family: "Anthropic Serif", serif !important;
}

.font-mono {
    font-family: "Anthropic Mono", monospace !important;
}

.font-lpmq {
    font-family: "LPMQ", serif !important;
    font-weight: normal;
    unicode-bidi: isolate;
    font-variant-ligatures: common-ligatures contextual;
    font-feature-settings: "liga", "calt", "rlig";
    line-height: 2.45;
}

.font-thuluth {
    font-family: "Thuluth", serif !important;
    font-weight: normal;
    unicode-bidi: isolate;
    font-variant-ligatures: common-ligatures contextual;
    font-feature-settings: "liga", "calt", "rlig";
    line-height: 2.45;
}

.font-arabic {
    direction: rtl;
    line-height: 2.45;
    unicode-bidi: isolate;
    font-variant-ligatures: common-ligatures contextual;
    font-feature-settings: "liga", "calt", "rlig";
}

.font-lpmq-lg {
    font-family: "LPMQ", serif !important;
    font-size: 2rem;
    line-height: 2.45;
    direction: rtl;
    font-variant-ligatures: common-ligatures contextual;
    font-feature-settings: "liga", "calt", "rlig";

}

.font-thuluth-lg {
    font-family: "Thuluth", serif !important;
    font-size: 2.2rem;
    line-height: 2.45;
    direction: rtl;
    unicode-bidi: isolate;
    font-variant-ligatures: common-ligatures contextual;
    font-feature-settings: "liga", "calt", "rlig";
}

:root {
    --primary: #d3ff4d;
    --primary-hover: #c2ef37;
    --primary-active: #aed72a;
    --primary-disabled: #e7ff9a;
}

.btn-primary {
    color: #212529 !important;
    background-color: var(--primary) !important;
    border-color: var(--primary) !important;
    font-weight: 600;
}

.btn-primary:hover,
.btn-primary.hover {
    color: #212529 !important;
    background-color: var(--primary-hover) !important;
    border-color: var(--primary-hover) !important;
}

.btn-primary:focus,
.btn-primary:focus-visible {
    color: #212529 !important;
    background-color: var(--primary-hover) !important;
    border-color: var(--primary-hover) !important;
    box-shadow: 0 0 0 .25rem rgba(211, 255, 77, .35) !important;
}

.btn-primary:active,
.btn-primary.active,
.show>.btn-primary.dropdown-toggle {
    color: #212529 !important;
    background-color: var(--primary-active) !important;
    border-color: var(--primary-active) !important;
}

.btn-primary:disabled,
.btn-primary.disabled {
    color: #212529 !important;
    background-color: var(--primary-disabled) !important;
    border-color: var(--primary-disabled) !important;
    opacity: .65;
}

.tooltip {
    .tooltip-inner {
        font-family: $font-secondary;
        font-size: 1rem;
        border-radius: 0 !important;
        background-color: $primary-color !important;
        color: var(--bs-body-color) !important;
        border: 1px solid var(--bs-dark);
    }
    .tooltip-arrow::before {
        display: none;
    }
}
```

- Update `src/services/surah.services.js`
```js
import api from "./api";

export const SurahService = {

    /**
     * Mengambil seluruh daftar surah.
     */
    async getAll() {
        const response = await api.get("/surat");
        return response.data.data;
    },

    /**
     * Mengambil detail satu surah.
     */
    async getByNumber(number) {
        const response = await api.get(`/surat/${number}`);
        return response.data.data;
    },

    async getById(nomor) {
        const { data } = await api.get(`/surat/${nomor}`);
        return data.data;
    }

};
```

- Update `src/App.jsx`
```jsx
import { Routes, Route } from "react-router-dom";

import Layout from "./layouts";
import Sidebar from "./layouts/Sidebar.jsx";
import {
  PrimaryOffcanvasButton
} from "./layouts/index.jsx";
import Brand from "./layouts/Brand.jsx";
import Home from "./pages/Home.jsx";
import Surah from "./pages/Surah.jsx";
import Ayah from "./pages/Ayah.jsx";

function App() {
  return (
    <Routes>
      <Route
        element={
          <>
            <header className="py-2 mb-4 position-sticky top-0 bg-body d-sm-none z-3">
              <div className="container">
                <div className="row">
                  <div className="col-12 d-flex align-items-center justify-content-between">
                    <Brand />
                    <PrimaryOffcanvasButton className="btn-dark rounded-pill text-nowrap text-uppercase">
                      <i className="bi bi-list me-2"></i>
                      Menu
                    </PrimaryOffcanvasButton>
                  </div>
                </div>
              </div>
            </header>
            <Layout
              primarySidebarContent={<Sidebar />}
            />
          </>
        }
      >
        <Route
          path="/"
          element={
            <>
              <Home />
            </>
          }
        />

        <Route
          path="/quran"
          element={<Surah/>}
        />

        <Route path="/surahs/:nomor" element={<Ayah />} />

        <Route
          path="/tajwid"
          element={<p>Halaman Highlight Tajwid</p>}
        />
      </Route>
    </Routes>
  );
}

export default App;
```

- Update `src/styles/index.scss`
```scss
@import url("https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/font/bootstrap-icons.min.css");

@import "./variables.scss";
@import "./layout.scss";
@import "./sidebar.scss";
@import "./brand.scss";
@import "./card.scss";
@import "./home.scss";
@import "./utils.scss";
@import "./surah.scss";
@import "./ayah.scss";
```

- Update `src/hooks/Tajwid.jsx`
```jsx
import { useEffect, useRef } from "react";
const { Tooltip } = window.bootstrap;

const arabicChars = {
    alif: "\u0627",
    ba: "\u0628",
    ta: "\u062A",
    tsa: "\u062B",
    jim: "\u062C",
    ha: "\u062D",
    kho: "\u062E",
    dal: "\u062F",
    dzal: "\u0630",
    ra: "\u0631",
    zai: "\u0632",
    sin: "\u0633",
    syin: "\u0634",
    shod: "\u0635",
    dhod: "\u0636",
    tho: "\u0637",
    zho: "\u0638",
    ain: "\u0639",
    ghain: "\u063A",
    fa: "\u0641",
    qof: "\u0642",
    kaf: "\u0643",
    lam: "\u0644",
    mim: "\u0645",
    nun: "\u0646",
    waw: "\u0648",
    ha2: "\u0647",
    hamzah: "\u0621",
    ya: "\u064A",

    maddah: "\u0653",
    small_madd: "\u06E4",

    fathah: "\u064E",
    kasrah: "\u0650",
    dhammah: "\u064F",
    sukun: "\u0652",
    tasydid: "\u0651",
    tanwin_a: "\u064B",
    tanwin_i: "\u064D",
    tanwin_u: "\u064C",
    fathah_alif: "\u0649",
    fathah_superscript: "\u0670",
    kasrah_superscript: "\u0656",
    dhammah_inverted: "\u0657",

    lam_alif: "\u0644\u0627",
    ta_marbuta: "\u0629",
    alif_mad: "\u0622",
    alif_hamzah_atas: "\u0623",
    alif_hamzah_bawah: "\u0625",
    hamzah_below: "\u0655",
    waw_hamzah: "\u0624",
    ya_hamzah: "\u0626",

    space: "\u0020"
};

const arabicGroups = {
    letters: [
        arabicChars.alif,
        arabicChars.ba,
        arabicChars.ta,
        arabicChars.tsa,
        arabicChars.jim,
        arabicChars.ha,
        arabicChars.kho,
        arabicChars.dal,
        arabicChars.dzal,
        arabicChars.ra,
        arabicChars.zai,
        arabicChars.sin,
        arabicChars.syin,
        arabicChars.shod,
        arabicChars.dhod,
        arabicChars.tho,
        arabicChars.zho,
        arabicChars.ain,
        arabicChars.ghain,
        arabicChars.fa,
        arabicChars.qof,
        arabicChars.kaf,
        arabicChars.lam,
        arabicChars.mim,
        arabicChars.nun,
        arabicChars.waw,
        arabicChars.ha2,
        arabicChars.hamzah,
        arabicChars.ya,
        arabicChars.ta_marbuta
    ].join(""),

    tanwin: [
        arabicChars.tanwin_a,
        arabicChars.tanwin_i,
        arabicChars.tanwin_u
    ].join(""),

    harakat: [
        arabicChars.fathah,
        arabicChars.kasrah,
        arabicChars.dhammah,
        arabicChars.sukun,
        arabicChars.tasydid
    ].join(""),

    diacritics: [
        arabicChars.fathah,
        arabicChars.kasrah,
        arabicChars.dhammah,
        arabicChars.sukun,
        arabicChars.tasydid,
        arabicChars.tanwin_a,
        arabicChars.tanwin_i,
        arabicChars.tanwin_u,
        arabicChars.maddah,
        arabicChars.small_madd,
        arabicChars.fathah_superscript,
        arabicChars.kasrah_superscript,
        arabicChars.dhammah_inverted,
        arabicChars.hamzah_below
    ].join(""),

    idzhar: [
        arabicChars.hamzah,
        arabicChars.ha2,
        arabicChars.ain,
        arabicChars.ha,
        arabicChars.ghain,
        arabicChars.kho
    ].join(""),

    idgham: {
        maal_gunnah: [
            arabicChars.ya,
            arabicChars.nun,
            arabicChars.mim,
            arabicChars.waw
        ].join(""),

        bila_gunnah: [
            arabicChars.lam,
            arabicChars.ra
        ].join(""),

        all: [
            arabicChars.ya,
            arabicChars.nun,
            arabicChars.mim,
            arabicChars.waw,
            arabicChars.lam,
            arabicChars.ra
        ].join("")
    },

    iqlab: arabicChars.ba,

    ikhfa: [
        arabicChars.ta,
        arabicChars.tsa,
        arabicChars.jim,
        arabicChars.dal,
        arabicChars.dzal,
        arabicChars.zai,
        arabicChars.sin,
        arabicChars.syin,
        arabicChars.shod,
        arabicChars.dhod,
        arabicChars.tho,
        arabicChars.zho,
        arabicChars.fa,
        arabicChars.qof,
        arabicChars.kaf
    ].join(""),

    marks: "\u0610-\u061A\u064B-\u0652\u0653-\u065F\u0670\u06D6-\u06ED",
    nonQuranicLetters: "\u06D5"
};

const tajwids = [
    {
        name: "Izhar Halqi",
        priority: 2,
        color: "rgb(179, 74, 0)",
        backgroundColor: "rgba(179, 74, 0, 0.1)",
        pattern: [
            [
                /** Nun + sukun */
                `${arabicChars.nun}${arabicChars.sukun}`,
                /** optional space */
                `(?:${arabicChars.space})?`,
                /** lookahead idzhar */
                `(?=[${arabicGroups.idzhar}])`
            ].join(""),

            [
                /** Letter + tanwin */
                `[${arabicGroups.letters}][${arabicGroups.tanwin}]`,
                /** Opional spasi */
                `(?:${arabicChars.space})?`,
                /** setelahnya huruf idzhar */
                `(?=[${arabicGroups.idzhar}])`
            ].join("")
        ],
        flags: "g",
        className: "idzhar"
    },
    {
        name: "Idgham Bigunnah",
        priority: 2,
        color: "rgb(0, 128, 0)",
        backgroundColor: "rgba(0, 128, 0, 0.1)",
        pattern: [
            [
                /** Nun sukun */
                `${arabicChars.nun}${arabicChars.sukun}`,
                /** optional space */
                `(?:${arabicChars.space})?`,
                /** Setelahnya Huruf idgham maal gunnah */
                `(?=[${arabicGroups.idgham.maal_gunnah}])`
            ].join(""),
            [
                /** Huruf dengan tanwin */
                `[${arabicGroups.letters}][${arabicGroups.tanwin}]`,
                /** Opsional alif */
                `${arabicChars.alif}?`,
                /** Opsional alif maksuroh */
                `(?:${arabicChars.fathah_alif})?`,
                /** optional space */
                `(?:${arabicChars.space})?`,
                /** Setelahnya huruf idghom maal gunnah */
                `(?=`,
                `[${arabicGroups.idgham.maal_gunnah}]`,
                `)`
            ].join("")
        ],
        flags: "g",
        className: "idgham"
    },
    {
        name: "Idgham Bilagunnah",
        priority: 2,
        color: "rgb(0, 128, 0)",
        backgroundColor: "rgba(0, 128, 0, 0.1)",
        pattern: [
            [
                /** Nun sukun */
                `${arabicChars.nun}${arabicChars.sukun}`,
                /** optional space */
                `(?:${arabicChars.space})?`,
                /** Setelahnya huruf idgham bila gunnah */
                `(?=`,
                `[${arabicGroups.idgham.bila_gunnah}]`,
                `)`
            ].join(""),
            [
                /** Huruf bertanwin */
                `[${arabicGroups.letters}][${arabicGroups.tanwin}]`,
                /** Opsional alif */
                `${arabicChars.alif}?`,
                /** Opsional alif maksuroh dan spasi */
                `${arabicChars.fathah_alif}?`,
                `${arabicChars.space}?`,
                /** Setelahnya huruf idghom bila gunnah */
                `(?=`,
                `[${arabicGroups.idgham.bila_gunnah}]`,
                `)`
            ].join("")
        ],
        flags: "g",
        className: "idgham"
    },
    {
        name: "Iqlab",
        priority: 2,
        color: "rgb(0, 128, 128)",
        backgroundColor: "rgba(0, 128, 128, 0.1)",
        pattern: [
            [
                /** Nun Sukun */
                `${arabicChars.nun}${arabicChars.sukun}`,
                /** Opsional spasi */
                `(?:${arabicChars.space})?`,
                /** Setelahnya huruf iqlab */
                `(?=`,
                `[${arabicGroups.iqlab}]`,
                `)`
            ].join(""),
            [
                /** Huruf bertanwin */
                `[${arabicGroups.letters}][${arabicGroups.tanwin}]`,
                /** Opsional spasi dn opsional mark */
                `(?:[${arabicGroups.marks}${arabicChars.space}${arabicGroups.nonQuranicLetters}]*)?`,
                /** Setelahnya huruf iqlab */
                `(?=`,
                `[${arabicGroups.iqlab}]`,
                `)`
            ].join("")
        ],
        flags: "g",
        className: "iqlab"
    },
    {
        name: "Ikhfa",
        priority: 3,
        color: "rgb(189, 15, 30)",
        backgroundColor: "rgba(189, 15, 30, 0.1)",
        pattern: [
            [
                /** Nun Sukun */
                `${arabicChars.nun}`,
                `${arabicChars.sukun}?`,
                /** Opsional spasi */
                `(?:${arabicChars.space})?`,
                /** Setelahnya huruf ikhfa */
                `(?=`,
                `[${arabicGroups.ikhfa}]`,
                `)`
            ].join(""),
            [
                /** Huruf bertanwin */
                `[${arabicGroups.letters}][${arabicGroups.tanwin}]`,
                `${arabicChars.alif}?`,
                /** Opsional spasi */
                `(?:${arabicChars.space})?`,
                /** Setelahnya huruf ikhfa */
                `(?=`,
                `[${arabicGroups.ikhfa}]`,
                `)`
            ].join("")
        ],
        flags: "g",
        className: "ikhfa"
    },
    {
        name: "Mad Tabi'i",
        priority: 4,
        color: "rgb(129, 0, 194)",
        backgroundColor: "rgba(129, 0, 194, 0.05)",
        pattern: [
            /** Fathah */
            [
                /** Sebelumnya adalah fathah */
                `(?<=`,
                `${arabicChars.fathah}`,
                `)`,
                /** Huruf alif */
                `${arabicChars.alif}`,
                /** Alif tidak idak memiliki harakat */
                `(?!`,
                `${arabicChars.fathah}|`,
                `${arabicChars.dhammah}|`,
                `${arabicChars.kasrah}|`,
                /** Setelah nya bukan lam */
                `${arabicChars.lam}|`,
                /** Setelahnya bukan mad */
                `${arabicChars.small_madd}|`,
                `${arabicChars.maddah}|`,
                /** Setelahnya bukan spasi dan alif lam */
                `${arabicChars.space}${arabicChars.alif}${arabicChars.lam}|`,
                /** Setelahnya bukan huruf bertasydid */
                `[${arabicGroups.letters}]${arabicChars.tasydid}`,
                `)`,
                /** Bukan akhir ayah(Harus memiliki setidaknya 4 karakter atau lebih banyak) */
                `(?=.{5,})`,
            ].join(""),
            /** Dhommah */
            [
                /** Sebelumnya dhommah */
                `(?<=`,
                `${arabicChars.dhammah}`,
                `)`,
                `(?<!${arabicChars.alif_hamzah_atas})`,
                /** Kemudian wawu sukun */
                `${arabicChars.waw}`,
                `${arabicChars.sukun}?`,
                /** Setellahnya bukan mad */
                `(?!`,
                `${arabicChars.small_madd}|`,
                `${arabicChars.maddah}|`,
                `\\u06DF`,
                `)`,
                /** Bukan akhir ayah(Harus memiliki setidaknya 4 karakter atau lebih banyak) */
                `(?=.{5,})`,
            ].join(""),
            /** Kasrah */
            [
                /** Sebelumnya harus kasrah */
                `(?<=`,
                `${arabicChars.kasrah}`,
                `)`,
                /** Ya sukun */
                `${arabicChars.ya}`,
                `${arabicChars.sukun}?`,
                /** Setelahnya bukan mad */
                `(?!`,
                `${arabicChars.small_madd}|`,
                `${arabicChars.maddah}`,
                `)`,
                /** Bukan akhir ayah(Harus memiliki setidaknya 4 karakter atau lebih banyak) */
                `(?=.{5,})`,
            ].join(""),
            [
                /** Huruf dengan fathah superscript */
                `[${arabicGroups.letters}]`,
                /** Opsioal tasydid */
                `${arabicChars.tasydid}?`,
                `${arabicChars.fathah}?`,
                `${arabicChars.fathah_superscript}`,
                /** Setalhnya bukan mad */
                `(?![${arabicChars.small_madd}${arabicChars.maddah}])`
            ].join(""),
        ],
        flags: "g",
        className: "mad-tabii"
    },
];

function resolveConflicts(matches) {
    if (matches.length === 0) return [];

    // Urutkan: start ASC, lalu priority ASC (lebih kecil = lebih tinggi)
    const sorted = [...matches].sort((a, b) => {
        if (a.start !== b.start) return a.start - b.start;
        return a.priority - b.priority;
    });

    const accepted = [];

    for (const candidate of sorted) {
        if (accepted.length === 0) {
            accepted.push(candidate);
            continue;
        }

        const last = accepted[accepted.length - 1];
        const overlaps = candidate.start < last.end;

        if (!overlaps) {
            // Tidak ada tumpang-tindih → langsung terima
            accepted.push(candidate);
        } else if (candidate.start === last.start) {
            // Dimulai di posisi sama → yang sudah di-accept punya priority
            // lebih tinggi (karena sort), jadi abaikan kandidat ini
        } else {
            // Overlap sebagian (candidate.start > last.start)
            // Ganti hanya jika kandidat punya priority LEBIH TINGGI (angka lebih kecil)
            if (candidate.priority < last.priority) {
                accepted.pop();
                accepted.push(candidate);
            }
            // Jika tidak lebih tinggi, abaikan kandidat
        }
    }

    return accepted;
}

export default function Tajwid({ text }) {
    const rawMatches = [];

    tajwids.forEach(({ pattern, flags, className, color, name, backgroundColor, priority }) => {
        // Normalisasi: jadikan array jika masih string tunggal
        const patterns = Array.isArray(pattern) ? pattern : [pattern];

        patterns.forEach((p) => {
            const regex = new RegExp(p, flags);

            text.replace(regex, (match, ...args) => {
                const offset = args[args.length - 2];

                rawMatches.push({
                    start: offset,
                    end: offset + match.length,
                    match,
                    className,
                    color,
                    name,
                    backgroundColor,
                    priority
                });
            });
        });
    });

    // Selesaikan konflik dengan sistem prioritas
    const matches = resolveConflicts(rawMatches);

    // Bangun output React
    const parts = [];
    let lastIndex = 0;

    matches.forEach((m, i) => {
        if (m.start > lastIndex) {
            parts.push(text.slice(lastIndex, m.start));
        }

        parts.push(
            <TajwidSpan
                key={`${m.start}-${i}`}
                {...m}
            />
        );

        lastIndex = m.end;
    });

    // Sisa teks setelah match terakhir
    if (lastIndex < text.length) {
        parts.push(text.slice(lastIndex));
    }

    return <>{parts}</>;
}

function TajwidSpan({ match, name, className, color, backgroundColor }) {
    const tooltipRef = useRef(null);

    useEffect(() => {
        if (!tooltipRef.current) return;

        const tooltip = new Tooltip(tooltipRef.current, {
            boundary: document.body
        });

        return () => tooltip.dispose(); // ✅ cleanup
    }, []);

    return (
        <span
            ref={tooltipRef}
            style={{
                backgroundColor: backgroundColor || undefined,
                color: color || undefined,
                borderColor: backgroundColor || undefined,
                cursor: "pointer"
            }}
            data-bs-toggle="tooltip"
            data-bs-custom-class="tajwid"
            data-bs-html="true"
            data-bs-placement="top"
            data-bs-title={`<small>${name}</small>`}
            className={`rounded-1 border-0 ${className}`}
        >
            {match}
        </span>
    );
}

/**
 * Menerima array penggalan teks Arab, menggabungkannya, melakukan highlight
 * tajwid, lalu memotong kembali sesuai panjang fragment asli.
 *
 * @param {string[]} fragments - Array penggalan teks Arab
 * @returns {Array<Array<string|JSX.Element>>} - Array of parts per fragment
 */
export function highlightAndSplit(fragments) {
    // ── 1. Gabungkan & catat offset tiap fragment ────────────────────────────
    const offsets = [];
    let joined = "";

    for (const frag of fragments) {
        offsets.push({ start: joined.length, end: joined.length + frag.length });
        joined += frag;
    }

    // ── 2. Jalankan tajwid matching pada string gabungan ─────────────────────
    const rawMatches = [];

    tajwids.forEach(({ pattern, flags, className, color, name, backgroundColor, priority }) => {
        const patterns = Array.isArray(pattern) ? pattern : [pattern];

        patterns.forEach((p) => {
            const regex = new RegExp(p, flags);

            joined.replace(regex, (match, ...args) => {
                const offset = args[args.length - 2];
                rawMatches.push({
                    start: offset,
                    end: offset + match.length,
                    match,
                    className,
                    color,
                    name,
                    backgroundColor,
                    priority
                });
            });
        });
    });

    const matches = resolveConflicts(rawMatches);

    // ── 3. Potong kembali sesuai batas tiap fragment ─────────────────────────
    return offsets.map(({ start: fragStart, end: fragEnd }, fragIdx) => {
        const parts = [];
        let cursor = fragStart;

        for (const m of matches) {
            // Lewati match yang sepenuhnya sebelum fragment ini
            if (m.end <= fragStart) continue;
            // Hentikan jika match sepenuhnya setelah fragment ini
            if (m.start >= fragEnd) break;

            // Kliping match ke batas fragment (jika span lintas fragment)
            const clipStart = Math.max(m.start, fragStart);
            const clipEnd   = Math.min(m.end,   fragEnd);

            // Teks biasa sebelum match
            if (clipStart > cursor) {
                parts.push(joined.slice(cursor, clipStart));
            }

            // Span tajwid (mungkin ter-klip jika lintas batas fragment)
            parts.push(
                <TajwidSpan
                    key={`${fragIdx}-${m.start}-${clipStart}`}
                    {...m}
                    match={joined.slice(clipStart, clipEnd)}
                />
            );

            cursor = clipEnd;
        }

        // Sisa teks biasa di akhir fragment
        if (cursor < fragEnd) {
            parts.push(joined.slice(cursor, fragEnd));
        }

        return parts;
    });
}
```

## Membuat halaman deteksi tajwid
- Update `src/pages/Editor.jsx`
```jsx
import { useRef, useState } from "react";
import Textarea from "../components/Textarea";
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

export default function Editor() {
    const containerRef = useRef(null);

    const [leftWidth, setLeftWidth] = useState(50);
    const [text, setText] = useState("");
    const [mode, setMode] = useState("input");

    function onlyArabic(text) {
        return text.replace(
            /[^\u0600-\u06FF\u0750-\u077F\u08A0-\u08FF\uFB50-\uFDFF\uFE70-\uFEFF\s]/g,
            ""
        );
    }

    const handleMouseDown = () => {
        const handleMouseMove = (e) => {
            if (!containerRef.current) return;

            const rect = containerRef.current.getBoundingClientRect();

            let newWidth =
                ((e.clientX - rect.left) / rect.width) * 100;

            // batas minimum dan maksimum panel kiri
            newWidth = Math.max(20, Math.min(80, newWidth));

            setLeftWidth(newWidth);
        };

        const handleMouseUp = () => {
            document.removeEventListener(
                "mousemove",
                handleMouseMove
            );

            document.removeEventListener(
                "mouseup",
                handleMouseUp
            );
        };

        document.addEventListener(
            "mousemove",
            handleMouseMove
        );

        document.addEventListener(
            "mouseup",
            handleMouseUp
        );
    };

    function handleChange(e) {
        const arabicText = onlyArabic(e.target.value);

        setText(arabicText);
    }

    const handlePaste = async () => {
        try {
            const clipboardText = await readClipboard();

            const arabicText = onlyArabic(clipboardText);

            if (!arabicText.trim()) {
                alert("Clipboard tidak berisi teks Arab");
                return;
            }

            setText((prev) =>
                prev
                    ? prev + "\n" + arabicText
                    : arabicText
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

    const handlePasteEvent = (e) => {
        e.preventDefault();

        const pastedText = e.clipboardData.getData("text");

        const arabicText = onlyArabic(pastedText);

        if (!arabicText.trim()) {
            return;
        }

        setText((prev) =>
            prev
                ? prev + "\n" + arabicText
                : arabicText
        );
    };

    const handleClear = () => {
        setText("");
    };

    const handleExampleText = () => {
        setText(
            "ذَٰلِكَ الْكِتَابُ لَا رَيْبَ فِيهِ هُدًى لِّلْمُتَّقِينَ"
        );
    };

    return (
        <>
            <section className="editor py-5 min-vh-100">
                <div className="container">

                    <div className="row justify-content-center">
                        <div className="col-11 col-sm-10 text-center">

                            <h1 className="display-2" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">
                                Deteksi{" "}
                                <span className="bg-dark text-white text-nowrap px-3 rounded-3">
                                    Tajwid
                                </span>
                            </h1>

                            <p className="fs-4" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">
                                Masukkan teks Arab Al-Qur'an untuk mendeteksi hukum tajwid dengan <em>highlight</em> otomatis pada setiap bacaan.
                            </p>

                        </div>
                    </div>


                    <div className="row justify-content-center g-1">
                        <div className="col-11 col-md-12 px-4">

                            <div className="card border border-secondary-subtle rounded-0 shadow-sm mx-2" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">

                                <div className="corner top left"></div>
                                <div className="corner top right"></div>
                                <div className="corner bottom left"></div>
                                <div className="corner bottom right"></div>


                                <div className="card-body p-0">

                                    <div
                                        className="editor-split"
                                        ref={containerRef}
                                    >

                                        <div
                                            className={`editor-panel d-md-block flex-grow-1 ${mode === "result" ? "d-none" : ""}`}
                                            style={{
                                                width: `${leftWidth}%`
                                            }}
                                        >
                                            <Textarea
                                                id="quran-editor"
                                                label="Masukkan Ayat"
                                                placeholder="Masukkan teks Arab"
                                                value={text}
                                                className="font-lpmq fs-4"
                                                onChange={handleChange}
                                                onPaste={handlePasteEvent}
                                            />
                                        </div>


                                        <div
                                            className="editor-divider"
                                            onMouseDown={handleMouseDown}
                                        />


                                        <div
                                            className={`editor-panel p-3 d-md-block flex-grow-1 ${mode === "input" ? "d-none" : ""}`}
                                            style={{
                                                width: `${100 - leftWidth}%`
                                            }}
                                        >
                                            {text ? (

                                                <p
                                                    dir="rtl"
                                                    className="fs-4 text-end font-lpmq"
                                                >
                                                    <Tajwid text={text} />
                                                </p>
                                            ) : (
                                                <p className="mb-0 font-secondary">
                                                    Belum ada teks arab yang dimasukkan
                                                </p>
                                            )}

                                        </div>

                                    </div>

                                </div>

                            </div>

                        </div>

                        <div className="col-11 text-center" data-aos="fade-up" data-aos-anchor-placement="bottom-bottom">
                            <small className="text-body-secondary">
                                Masukkan teks Arab Al-Qur'an dengan harakat untuk mendapatkan hasil deteksi tajwid yang lebih akurat.
                            </small>
                        </div>

                        <div data-aos="fade-up" data-aos-anchor-placement="bottom-bottom" className="col-9 col-sm-7 col-md-6 col-lg-4 col-xl-3 mt-3 d-flex align-items-center justify-content-center gap-2">
                            <button className="btn btn-primary btn-stylish rounded-0 w-100" onClick={handlePaste}>
                                <i className="bi bi-clipboard me-2"></i>
                                Tempel Teks
                            </button>
                        </div>
                        <div data-aos="fade-up" data-aos-anchor-placement="bottom-bottom" className="col-9 col-sm-7 col-md-6 col-lg-4 col-xl-3 mt-3 d-flex align-items-center justify-content-center gap-2">
                            <button className="btn btn-primary btn-stylish rounded-0 w-100" onClick={handleClear} disabled={!text}>
                                <i className="bi bi-trash3 me-2"></i>
                                Hapus
                            </button>
                        </div>
                        <div data-aos="fade-up" data-aos-anchor-placement="bottom-bottom" className="col-9 col-sm-7 col-md-6 col-lg-4 col-xl-3 mt-3 d-flex align-items-center justify-content-center gap-2">
                            <button className="btn btn-primary btn-stylish rounded-0 w-100" onClick={handleExampleText}>
                                <i className="bi bi-file-earmark-text me-2"></i>
                                Contoh Teks
                            </button>
                        </div>
                    </div>

                </div>
            </section>
            <section className="py-2 editor position-sticky bottom-0 d-lg-none border-top bg-body">
                <div className="container">
                    <div className="row">
                        <div className="col-12 d-flex align-items-center justify-content-center gap-2">
                            <button
                                className={`btn rounded-0 ${mode === "input"
                                    ? "btn-primary"
                                    : "btn-outline-dark"
                                    }`}
                                onClick={() => setMode("input")}
                            >
                                Input
                            </button>


                            <button
                                className={`btn rounded-0 ${mode === "result"
                                    ? "btn-primary"
                                    : "btn-outline-dark"
                                    }`}
                                onClick={() => setMode("result")}
                            >
                                Hasil Deteksi
                            </button>
                        </div>
                    </div>
                </div>
            </section>
        </>
    );
}
```

- Update `src/styles/editor.scss`
```scss
.editor {
    font-family: $font-tertiary;

    .container {
        max-width: 46rem;
    }

    h1,
    h3,
    .btn {
        font-family: $font-secondary;
    }

    &:nth-child(1) {
        background-image: linear-gradient(90deg, rgb(240, 240, 240) 1px, rgba(0, 0, 0, 0) 1px), linear-gradient(rgb(240, 240, 240) 1px, rgba(0, 0, 0, 0) 1px);
        background-size: 4rem 4rem;
    }

    .editor-split {
        display: flex;
        width: 100%;
        overflow: hidden;
    }


    .editor-panel {
        height: 100%;
        overflow: hidden;
    }


    .editor-panel textarea {
        width: 100%;
        height: 100%;
        resize: none;
        padding: 1.5rem;
        outline: none;
    }


    .editor-divider {
        width: 1px;
        flex-shrink: 0;
        background-color: var(--bs-border-color);
        cursor: col-resize;
        position: relative;
    }


    .editor-divider:hover {
        background-color: #6c757d;
    }


    /* area drag lebih besar agar mudah digunakan */
    .editor-divider::before {
        content: "";
        position: absolute;
        inset: 0 -5px;
        cursor: col-resize;
    }

    .form-control::placeholder {
        font-family: $font-secondary;
        font-size: 1rem;
        text-align: left;
        line-height: normal;
    }

    .font-secondary {
        font-family: $font-secondary;
    }

    .btn-stylish {
        display: inline-block;
        position: relative;
        background-color: transparent !important;
        text-decoration: none;
        overflow: hidden;
        z-index: 1;
        border: 1px solid var(--bs-dark) !important;

        &::before {
            content: "";
            position: absolute;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background-color: $primary-color;
            transform: translateX(-100%);
            transition: all .3s;
            z-index: -1;
        }

        &:hover::before {
            transform: translateX(0);
        }
    }
}
```

- Update `src/styles/index.scss`
```scss
@import url("https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/font/bootstrap-icons.min.css");

@import "./variables.scss";
@import "./layout.scss";
@import "./sidebar.scss";
@import "./brand.scss";
@import "./card.scss";
@import "./home.scss";
@import "./utils.scss";
@import "./surah.scss";
@import "./ayah.scss";
@import "./editor.scss"
```

- Update `src/App.jsx`
```jsx
import { Routes, Route } from "react-router-dom";

import Layout from "./layouts";
import Sidebar from "./layouts/Sidebar.jsx";
import {
  PrimaryOffcanvasButton
} from "./layouts/index.jsx";
import Brand from "./layouts/Brand.jsx";
import Home from "./pages/Home.jsx";
import Surah from "./pages/Surah.jsx";
import Ayah from "./pages/Ayah.jsx";
import Editor from "./pages/Editor.jsx";

import useAndroidBackButton from "./hooks/useAndroidBackButton.jsx";

function App() {
  useAndroidBackButton();

  return (
    <Routes>
      <Route
        element={
          <>
            <header className="py-2 mb-4 position-sticky top-0 bg-body d-sm-none z-3">
              <div className="container">
                <div className="row">
                  <div className="col-12 d-flex align-items-center justify-content-between">
                    <Brand />
                    <PrimaryOffcanvasButton className="btn-dark rounded-pill text-nowrap text-uppercase">
                      <i className="bi bi-list me-2"></i>
                      Menu
                    </PrimaryOffcanvasButton>
                  </div>
                </div>
              </div>
            </header>
            <Layout
              primarySidebarContent={<Sidebar />}
            />
          </>
        }
      >
        <Route
          path="/"
          element={
            <>
              <Home />
            </>
          }
        />

        <Route
          path="/quran"
          element={<Surah/>}
        />

        <Route path="/surahs/:nomor" element={<Ayah />} />

        <Route
          path="/tajwid"
          element={<Editor/>}
        />
      </Route>
    </Routes>
  );
}

export default App;
```

- Update `src/hooks/useAndroidBackButton.jsx`
```jsx
import { useEffect } from "react";
import { App } from "@capacitor/app";
import { useNavigate } from "react-router-dom";

export default function useAndroidBackButton() {
    const navigate = useNavigate();

    useEffect(() => {
        const handler = App.addListener(
            "backButton",
            ({ canGoBack }) => {

                if (canGoBack) {
                    navigate(-1);
                } else {
                    App.exitApp();
                }

            }
        );


        return () => {
            handler.remove();
        };

    }, [navigate]);
}
```

## Konversi ke mobile App
- Pasang Capacitor
```bash
npm install @capacitor/core @capacitor/cli @capacitor/android
```

- Build React
```bash
npm run build
```

- Tambahkan platform android
```bash
npx cap add android
```

- Sync
```bash
npx cap sync android
```

- Buka Android studio
```jsx
npx cap open android
```

## Jika ada perubahan coding
- Build React
```jsx
npm run build
```

- Copy Perubahan ke android
```jsx
npx cap sync android
```

