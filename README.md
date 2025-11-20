# BDB AI Website - Technical Knowledge Transfer Guide

<div style="background-color: #e3f2fd; border: 2px solid #2196f3; border-radius: 8px; padding: 20px; margin: 20px 0; text-align: center;">
  <h3 style="margin-top: 0; color: #1976d2;">📄 Download PDF Version</h3>
  <p style="margin: 10px 0;">Click the button below to download this document as PDF</p>
  <button onclick="window.print()" style="background-color: #2196f3; color: white; border: none; padding: 12px 30px; font-size: 16px; border-radius: 5px; cursor: pointer; font-weight: bold; box-shadow: 0 2px 4px rgba(0,0,0,0.2);">
    🖨️ Download as PDF
  </button>
  <p style="margin: 10px 0; font-size: 14px; color: #666;">
    <strong>Instructions:</strong> Click the button above, then select "Save as PDF" as the destination when the print dialog appears.
  </p>
  <p style="margin: 5px 0; font-size: 12px; color: #999;">
    Or press <kbd style="background-color: #f5f5f5; padding: 4px 8px; border-radius: 3px; border: 1px solid #ddd;">Ctrl+P</kbd> (Windows) / <kbd style="background-color: #f5f5f5; padding: 4px 8px; border-radius: 3px; border: 1px solid #ddd;">Cmd+P</kbd> (Mac)
  </p>
</div>

## Purpose
This document is designed for technical team members who will be working on the BDB AI website project. It provides practical knowledge about the codebase structure, development workflow, and how to make changes.

---

## 📚 Table of Contents
1. [Quick Start](#quick-start)
2. [Project Architecture](#project-architecture)
3. [File Structure & Organization](#file-structure--organization)
4. [Key Files & Their Purposes](#key-files--their-purposes)
5. [How Things Work](#how-things-work)
6. [Development Workflow](#development-workflow)
7. [Common Tasks](#common-tasks)
8. [Code Patterns & Conventions](#code-patterns--conventions)
9. [Troubleshooting](#troubleshooting)
10. [Important Notes](#important-notes)

---

## 🚀 Quick Start

### Prerequisites
- **Docker Desktop** installed and running (Recommended)
- Basic knowledge of HTML, CSS, JavaScript
- Understanding of jQuery
- Familiarity with Bootstrap 5
- Bash terminal (Git Bash on Windows, or native bash on Mac/Linux)

### First Time Setup

#### **Option 1: Docker with Nginx (Recommended - Production-like Environment)**

This is the **primary method** used by the team. It runs the project in a Docker container with nginx, matching the production environment.

1. **Ensure Docker Desktop is Running**
   - Start Docker Desktop application
   - Wait for it to fully start (whale icon in system tray)

2. **Navigate to Project Directory** (if needed)
   ```bash
   cd "C:/Users/DELL/Documents/BDB Website/bdb_ai/bdb_ai"
   ```

3. **Run Docker Container**
   ```bash
   docker run -p 80:80 \
     -v "C:/Users/DELL/Documents/BDB Website/bdb_ai/bdb_ai/BDB.AI/WebContent:/usr/share/nginx/html" \
     -v "C:/Users/DELL/Documents/BDB Website/bdb_ai/bdb_ai/nginx.conf:/etc/nginx/nginx.conf" \
     --name my_nginx_container \
     -d nginx
   ```

   **Command Breakdown:**
   - `-p 80:80`: Maps port 80 from container to host (access via `http://localhost`)
   - `-v "WebContent:/usr/share/nginx/html"`: Mounts WebContent folder as nginx web root
   - `-v "nginx.conf:/etc/nginx/nginx.conf"`: Mounts custom nginx configuration
   - `--name my_nginx_container`: Names the container for easy reference
   - `-d`: Runs in detached mode (background)
   - `nginx`: Uses official nginx Docker image

4. **Open in Browser**
   ```
   http://localhost/index.html
   ```
   or simply:
   ```
   http://localhost/
   ```

5. **Verify Setup**
   - Check that header loads (top navigation)
   - Check that footer loads (bottom)
   - Verify carousel works on homepage
   - Check browser console for errors

6. **Useful Docker Commands**
   ```bash
   # Stop the container
   docker stop my_nginx_container
   
   # Start the container (if stopped)
   docker start my_nginx_container
   
   # Remove the container (if you need to recreate)
   docker rm my_nginx_container
   
   # View container logs
   docker logs my_nginx_container
   
   # Check if container is running
   docker ps
   ```

**Note:** If port 80 is already in use, you can change it:
```bash
# Use port 8080 instead
docker run -p 8080:80 ...
# Then access via http://localhost:8080
```

**About nginx.conf:**
- The Docker command mounts a custom `nginx.conf` file
- This file should be in the parent directory: `C:/Users/DELL/Documents/BDB Website/bdb_ai/bdb_ai/nginx.conf`
- If the file doesn't exist, you can:
  1. Remove the `-v "nginx.conf:/etc/nginx/nginx.conf"` part from the command (uses default nginx config)
  2. Create a basic `nginx.conf` file (see example below)

**Basic nginx.conf Example** (if needed):
```nginx
events {
    worker_connections 1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    
    server {
        listen 80;
        server_name localhost;
        root /usr/share/nginx/html;
        index index.html;
        
        location / {
            try_files $uri $uri/ /index.html;
        }
    }
}
```

**Important:** Some features (like dynamic header/footer loading) may not work properly with simple HTTP servers due to CORS restrictions. Docker with nginx is recommended for full functionality.

---

## 🏗️ Project Architecture

### Technology Stack
- **HTML5**: Structure and content
- **CSS3**: Styling (Bootstrap 5.3.0 + custom CSS)
- **JavaScript**: 
  - jQuery (DOM manipulation)
  - Vanilla JS (ES5/ES6)
  - Bootstrap JS (components)
- **Libraries**:
  - Slick Carousel (image sliders)
  - WOW.js (scroll animations)
  - Bootstrap Icons (icon font)

### Architecture Pattern
- **Static Website**: No build process, no backend
- **Component-Based**: Reusable header/footer components
- **Modular CSS/JS**: Separate files for different functionalities
- **Progressive Enhancement**: Works without JavaScript, enhanced with it
- **Containerized Deployment**: Runs in Docker with nginx for production-like environment

### Key Architectural Decisions
1. **Dynamic Component Loading**: Header and footer loaded via jQuery `.load()`
2. **Version Query Strings**: Cache busting with `?v=26febv1`
3. **Lazy Loading**: Videos load only when in viewport
4. **Mobile-First**: Responsive design starting from mobile
5. **Docker + Nginx**: Production-like local development environment

---

## 📁 File Structure & Organization

### Root Directory Structure
```
WebContent/
├── index.html              # Main homepage (entry point)
├── header1.html            # Header component (loaded dynamically)
├── footer1.html            # Footer component (loaded dynamically)
├── contactus.html          # Contact page
├── [200+ other HTML pages] # Product pages, blog posts, etc.
│
├── resources1/            # PRIMARY resources (use this)
│   ├── css/               # Stylesheets
│   ├── js/                # JavaScript files
│   ├── images/            # Image assets
│   └── video/             # Video assets
│
└── resources/             # LEGACY resources (older code)
    ├── angularjs/         # AngularJS controllers (legacy)
    ├── css/               # Older stylesheets
    └── js/                # Older scripts
```

### Important: Use `resources1/` Not `resources/`
- **`resources1/`**: Current, actively used resources
- **`resources/`**: Legacy code, may contain outdated implementations

---

## 🔑 Key Files & Their Purposes

### HTML Files

#### `index.html` (Main Entry Point)
- **Purpose**: Homepage of the website
- **Key Sections**:
  - Lines 46: Header placeholder (loaded dynamically)
  - Lines 49-630: Banner carousel (11 slides)
  - Lines 632-780: Customer logos section
  - Lines 782-874: About BDB section
  - Lines 876-1235: Industry solutions tabs
  - Lines 1237-1291: Partner ecosystem
  - Lines 1293-1540: E2E platform features
  - Lines 1542-1742: Data facts/statistics
  - Lines 1745: Footer placeholder (loaded dynamically)
  - Lines 1747-1767: Script includes

#### `header1.html`
- **Purpose**: Navigation bar component
- **Usage**: Loaded into every page via jQuery
- **Key Features**:
  - Bootstrap 5 navbar
  - Dropdown menus for Products, Solutions
  - Mobile hamburger menu
  - Active state management

#### `footer1.html`
- **Purpose**: Footer component
- **Usage**: Loaded into every page via jQuery
- **Key Features**:
  - Company information
  - Social media links
  - Quick navigation links

### CSS Files (`resources1/css/`)

#### `common_styles.css`
- **Purpose**: Global styles used across all pages
- **Contains**: Base typography, common classes, utility styles
- **When to Edit**: Global style changes, brand colors, typography

#### `index.css`
- **Purpose**: Homepage-specific styles
- **Contains**: Carousel styles, homepage sections, custom layouts
- **When to Edit**: Homepage design changes

#### `menu.css`
- **Purpose**: Navigation menu styles
- **Contains**: Header, navbar, dropdown styles
- **When to Edit**: Navigation design changes

#### `footer.css`
- **Purpose**: Footer component styles
- **Contains**: Footer layout, links, social icons
- **When to Edit**: Footer design changes

#### `bootstrap.min.css`
- **Purpose**: Bootstrap 5 framework
- **Note**: Don't edit directly - override in custom CSS

### JavaScript Files (`resources1/js/`)

#### `jquery.js`
- **Purpose**: jQuery library (v3.x)
- **Usage**: Required for most functionality
- **Note**: Must load before other scripts

#### `common_script.js`
- **Purpose**: Shared utility functions
- **Key Functions**:
  ```javascript
  scrollFunction()        // Header scroll behavior
  relocate_contact()      // Redirect to contact page
  relocate_dashboard()   // Redirect to dashboard
  ```
- **When to Edit**: Adding global utility functions

#### `index.js`
- **Purpose**: Homepage-specific functionality
- **Key Features**:
  - Slick carousel initialization (4 carousels)
  - Counter animations on scroll
  - Card slider for platform features
  - Tab navigation scrolling
  - Lazy video loading
  - Read more/less toggles
- **When to Edit**: Homepage interactivity changes

#### `bootstrap.min.js`
- **Purpose**: Bootstrap 5 JavaScript components
- **Usage**: Required for carousel, dropdowns, modals
- **Note**: Don't edit directly

#### `slick.js`
- **Purpose**: Slick Carousel library
- **Usage**: Customer logos, partner logos, card sliders
- **Configuration**: Done in `index.js`

#### `wow.min.js`
- **Purpose**: Scroll animation library
- **Usage**: Animate elements when they enter viewport
- **Initialization**: `new WOW().init()` in index.html

---

## ⚙️ How Things Work

### 1. Dynamic Component Loading

**How Header/Footer Load:**
```javascript
// In index.html (and other pages)
$(document).ready(function () {
  $(".header").load("header1?v=26febv1");
  $(".footer").load("footer1?v=26febv1");
});
```

**How It Works:**
- jQuery `.load()` fetches HTML from `header1.html` and `footer1.html`
- Inserts content into `.header` and `.footer` divs
- Version query string (`?v=26febv1`) prevents caching

**To Update Header/Footer:**
- Edit `header1.html` or `footer1.html`
- Changes appear on ALL pages automatically
- Update version number to force cache refresh

### 2. Carousel System

**Main Banner Carousel (Bootstrap):**
```html
<!-- In index.html -->
<div id="carouselExampleSlidesOnly" class="carousel slide" data-bs-ride="carousel">
  <div class="carousel-inner">
    <div class="carousel-item active">...</div>
    <div class="carousel-item">...</div>
  </div>
</div>
```
- Uses Bootstrap 5 carousel component
- 11 slides total
- Auto-plays, has indicators
- No JavaScript initialization needed (Bootstrap handles it)

**Customer Logo Carousels (Slick):**
```javascript
// In index.js
$('.center').slick({
  arrows: false,
  centerMode: true,
  autoplay: true,
  speed: 7000,
  slidesToShow: 5,
  responsive: [...]
});
```
- Uses Slick.js library
- 4 separate carousels: `.center`, `.center2`, `.center3`, `.center4`
- Infinite scroll, auto-play
- Responsive breakpoints

### 3. Counter Animations

**How It Works:**
```javascript
// In index.js
$(window).scroll(function() {
  var oTop = $('#counter').offset().top - window.innerHeight;
  if ($(window).scrollTop() > oTop) {
    $('.counter-value1').each(function() {
      // Animate counter from 0 to data-count value
    });
  }
});
```

**HTML Structure:**
```html
<span class="counter counter-value1" data-count="125">0</span>+
```

**To Add New Counter:**
1. Add HTML with `counter` class and `data-count` attribute
2. Add corresponding counter animation in `index.js`
3. Different counter classes (value1, value2, etc.) have different animation durations

### 4. Lazy Video Loading

**How It Works:**
```javascript
// In index.js
var lazyVideos = document.querySelectorAll("video.lazy");
var lazyVideoObserver = new IntersectionObserver(function(entries) {
  entries.forEach(function(video) {
    if (video.isIntersecting) {
      video.target.children[0].src = video.target.children[0].dataset.src;
      video.target.load();
    }
  });
});
```

**HTML Structure:**
```html
<video class="video1 lazy" autoplay muted loop>
  <source data-src="path/to/video.mp4" type="video/mp4" />
</video>
```

**Key Points:**
- Videos with `.lazy` class don't load initially
- `data-src` instead of `src` on `<source>` tag
- IntersectionObserver watches for viewport entry
- Video loads when it enters viewport

### 5. Tab Navigation

**Industry Solutions Tabs:**
```html
<ul class="nav nav-tabs">
  <li><a data-bs-toggle="tab" href="#automobile">Automobile</a></li>
  <li><a data-bs-toggle="tab" href="#retail">Retail</a></li>
</ul>
<div class="tab-content">
  <div id="automobile" class="tab-pane active">...</div>
  <div id="retail" class="tab-pane">...</div>
</div>
```

**Horizontal Scrolling:**
```javascript
// In index.js
$('.right').on('click', function(){
  $('.nav-tabs').animate({scrollLeft: '+=200px'}, 300);
});
```
- Bootstrap tabs for content switching
- Custom JavaScript for horizontal scroll buttons
- Scroll buttons show/hide based on scroll position

### 6. Version Control (Cache Busting)

**How It Works:**
```html
<link rel="stylesheet" href="resources1/css/index.css?v=26febv1" />
<script src="resources1/js/index.js?v=26febv1"></script>
```

**Why It's Used:**
- Browsers cache CSS/JS files
- Query string changes force browser to fetch new version
- Update version number when making changes

**To Update:**
- Change `?v=26febv1` to `?v=NEWVERSION`
- Update in all HTML files that reference the file
- Or use find/replace across project

---

## 🔧 Development Workflow

### Making Changes to Existing Pages

1. **Identify the File**
   - Homepage: `index.html`
   - Other pages: Check URL structure (e.g., `/retail` → `retail.html`)

2. **Make Your Changes**
   - Edit HTML for content/structure
   - Edit CSS for styling
   - Edit JS for behavior

3. **Test Locally**
   - Refresh browser (hard refresh: Ctrl+F5)
   - Test on different screen sizes
   - Check browser console for errors

4. **Update Version Numbers** (if CSS/JS changed)
   - Update query strings in HTML files
   - Force cache refresh

5. **Commit Changes**
   - Use version control (Git, SVN, etc.)

### Adding New Pages

1. **Create HTML File**
   ```html
   <!DOCTYPE html>
   <html lang="en-US">
   <head>
     <title>New Page - BDB</title>
     <!-- Include CSS files -->
   </head>
   <body>
     <div class="header"></div>
     <!-- Your content here -->
     <div class="footer"></div>
     <!-- Include JS files -->
     <script>
       $(document).ready(function () {
         $(".header").load("header1?v=26febv1");
         $(".footer").load("footer1?v=26febv1");
       });
     </script>
   </body>
   </html>
   ```

2. **Add Page-Specific CSS** (if needed)
   - Create new CSS file in `resources1/css/`
   - Link in HTML `<head>`

3. **Add Page-Specific JS** (if needed)
   - Create new JS file in `resources1/js/`
   - Include before closing `</body>`

4. **Update Navigation** (if needed)
   - Edit `header1.html` to add menu item

### Modifying Header/Footer

1. **Edit Component File**
   - Header: `header1.html`
   - Footer: `footer1.html`

2. **Test**
   - Changes appear on ALL pages
   - Check multiple pages to verify

3. **Update Version** (if needed)
   - Update version in all pages that load header/footer

### Adding New Carousel Slide

1. **Add HTML Structure**
   ```html
   <div class="carousel-item carous--bg_X">
     <!-- Slide content -->
   </div>
   ```

2. **Add Indicator Button**
   ```html
   <button type="button" data-bs-target="#carouselExampleSlidesOnly" 
           data-bs-slide-to="10" class="carous-btn" aria-label="Slide 11">
   </button>
   ```

3. **Add Background Image** (if needed)
   - Add to `resources1/images/background/`
   - Reference in CSS or inline style

4. **Test**
   - Verify slide appears
   - Check navigation works

### Adding New Customer Logo

1. **Add Image**
   - Place logo in `resources1/images/out_of_fortune_500/`
   - Optimize image (PNG, SVG, or WebP)

2. **Add to HTML**
   ```html
   <li class="lnd-slick__card">
     <img class="mx-auto global-customer-icon" 
          src="resources1/images/out_of_fortune_500/new-logo.png" 
          alt="Company Name" />
   </li>
   ```

3. **Add to Appropriate Carousel**
   - `.center`, `.center2`, `.center3`, or `.center4`
   - Distribute evenly if possible

---

## 📝 Common Tasks

### Task 1: Update Homepage Content

**Steps:**
1. Open `index.html`
2. Find the section (use browser DevTools to inspect)
3. Edit HTML content
4. Save and refresh

**Example - Update Carousel Text:**
```html
<!-- Find carousel item -->
<div class="carousel-item carous--bg_6 active">
  <h1 class="slider-title--heading text-white">Your New Text</h1>
</div>
```

### Task 2: Change Colors/Branding

**Steps:**
1. Open `resources1/css/common_styles.css`
2. Find color definitions (search for hex codes)
3. Update colors
4. Update version number in HTML files

**Common Color Locations:**
- Primary brand color: Search for `#007bff` or similar
- Text colors: `.text-white`, `.text-dark`, etc.
- Background colors: `.bg-*` classes

### Task 3: Add New Industry Solution Tab

**Steps:**
1. Open `index.html`
2. Find tab navigation (around line 893)
3. Add new tab button:
   ```html
   <li class="tap-pane-li">
     <a data-bs-toggle="tab" href="#newindustry" class="different-vertical__title">
       <i class="bi bi-icon-name vertical-icons"></i>
       New Industry
     </a>
   </li>
   ```
4. Add tab content:
   ```html
   <div id="newindustry" class="tab-pane">
     <!-- Content here -->
   </div>
   ```

### Task 4: Update Statistics Numbers

**Steps:**
1. Open `index.html`
2. Find statistics section (around line 1551)
3. Update `data-count` attribute:
   ```html
   <span class="counter counter-value1" data-count="150">0</span>+
   ```
4. Update display text if needed

### Task 5: Fix Broken Image

**Steps:**
1. Check image path in HTML
2. Verify image exists in `resources1/images/`
3. Check file name (case-sensitive)
4. Update path if needed

**Common Issues:**
- Wrong folder path
- Missing file extension
- Case sensitivity (Windows vs Linux)

### Task 6: Add New CSS Style

**Steps:**
1. Determine scope:
   - Global: `common_styles.css`
   - Homepage: `index.css`
   - Component: Component-specific CSS file
2. Add CSS rule
3. Update version number if needed

**Example:**
```css
/* In common_styles.css */
.my-new-class {
  color: #333;
  padding: 20px;
}
```

### Task 7: Add New JavaScript Function

**Steps:**
1. Determine scope:
   - Global: `common_script.js`
   - Homepage: `index.js`
   - Page-specific: Create new JS file
2. Add function
3. Call function where needed

**Example:**
```javascript
// In common_script.js
function myNewFunction() {
  // Your code here
}

// Call it
$(document).ready(function() {
  myNewFunction();
});
```

---

## 🎨 Code Patterns & Conventions

### HTML Conventions

1. **Indentation**: 2 spaces (not tabs)
2. **Class Naming**: 
   - Use descriptive names: `.slider-title--heading`
   - BEM-like pattern: `.block__element--modifier`
3. **IDs**: Use for unique elements (carousel, counters)
4. **Semantic HTML**: Use proper tags (`<section>`, `<article>`, etc.)

### CSS Conventions

1. **Organization**: Group related styles
2. **Specificity**: Avoid overly specific selectors
3. **Responsive**: Use Bootstrap classes first, custom media queries second
4. **Comments**: Comment complex sections

**Example:**
```css
/* Carousel Styles */
.carous--bg_6 {
  background-image: url('../images/background/bg6.png');
  background-size: cover;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .carous--bg_6 {
    background-position: center;
  }
}
```

### JavaScript Conventions

1. **jQuery Ready**: Always wrap in `$(document).ready()`
2. **Variable Naming**: camelCase
3. **Function Organization**: Group related functions
4. **Comments**: Comment complex logic

**Example:**
```javascript
$(document).ready(function() {
  // Initialize carousel
  $('.center').slick({
    // config
  });
  
  // Handle scroll events
  $(window).scroll(function() {
    // logic
  });
});
```

### File Naming Conventions

- **HTML**: lowercase with hyphens: `data-pipeline.html`
- **CSS**: lowercase with hyphens: `common-styles.css` (but current files use underscores)
- **JS**: lowercase with hyphens: `common-script.js` (but current files use underscores)
- **Images**: descriptive names: `banner-data-agent.png`

---

## 🐛 Troubleshooting

### Issue: Docker Container Won't Start

**Symptoms:**
- `docker run` command fails
- Port already in use error
- Permission denied errors

**Solutions:**

1. **Port 80 Already in Use**
   ```bash
   # Check what's using port 80
   netstat -ano | findstr :80
   
   # Use different port
   docker run -p 8080:80 ...
   # Access via http://localhost:8080
   ```

2. **Container Name Already Exists**
   ```bash
   # Remove existing container
   docker rm my_nginx_container
   
   # Or use different name
   docker run --name new_container_name ...
   ```

3. **Docker Not Running**
   - Ensure Docker Desktop is started
   - Check Docker Desktop status in system tray
   - Restart Docker Desktop if needed

4. **Path Issues (Windows)**
   - Use forward slashes `/` or escaped backslashes `\\`
   - Ensure paths are absolute (start with drive letter)
   - Check path exists: `dir "C:/Users/DELL/Documents/BDB Website/..."`

5. **nginx.conf Not Found**
   - Verify `nginx.conf` exists in parent directory
   - Check path in Docker command matches actual location
   - If nginx.conf doesn't exist, you can create a basic one or remove that volume mount

**Fix:**
```bash
# Basic command without custom nginx.conf
docker run -p 80:80 \
  -v "C:/Users/DELL/Documents/BDB Website/bdb_ai/bdb_ai/BDB.AI/WebContent:/usr/share/nginx/html" \
  --name my_nginx_container \
  -d nginx
```

### Issue: Changes Not Reflecting in Browser

**Symptoms:**
- Edited files but browser shows old content
- Hard refresh doesn't help

**Solutions:**

1. **Docker Volume Mount Issue**
   - Ensure volume mount path is correct
   - Verify you're editing files in the mounted directory
   - Check file permissions

2. **Browser Cache**
   - Hard refresh: `Ctrl + F5` (Windows) or `Cmd + Shift + R` (Mac)
   - Clear browser cache
   - Use incognito/private window

3. **Nginx Cache**
   ```bash
   # Restart container to clear nginx cache
   docker restart my_nginx_container
   ```

4. **File Not Saved**
   - Verify file is saved in editor
   - Check file modification time
   - Ensure you're editing the correct file

### Issue: Header/Footer Not Loading

**Symptoms:**
- Empty space where header/footer should be
- Console errors about jQuery

**Solutions:**
1. Check jQuery is loaded before load script
2. Verify file paths: `header1.html`, `footer1.html`
3. Check browser console for 404 errors
4. Verify server is running (can't load via `file://`)
5. **With Docker**: Check nginx error logs: `docker logs my_nginx_container`

**Fix:**
```html
<!-- Correct order -->
<script src="resources1/js/jquery.js"></script>
<script>
  $(document).ready(function () {
    $(".header").load("header1?v=26febv1");
  });
</script>
```

**Docker-Specific Check:**
```bash
# Check nginx logs for errors
docker logs my_nginx_container

# Verify files are accessible
docker exec my_nginx_container ls -la /usr/share/nginx/html/
```

### Issue: Carousel Not Working

**Symptoms:**
- Slides not changing
- No navigation arrows
- Console errors

**Solutions:**
1. Check Bootstrap JS is loaded
2. Verify carousel HTML structure
3. Check for JavaScript errors in console
4. Verify `data-bs-ride="carousel"` attribute

**Fix:**
```html
<!-- Ensure Bootstrap JS is loaded -->
<script src="resources1/js/bootstrap.min.js"></script>
```

### Issue: Counter Animation Not Triggering

**Symptoms:**
- Numbers don't animate
- Stuck at 0

**Solutions:**
1. Check `#counter` element exists
2. Verify counter classes match JS selectors
3. Check scroll event is firing
4. Verify `data-count` attributes are set

**Fix:**
```javascript
// Add console.log to debug
$(window).scroll(function() {
  console.log('Scroll event fired');
  var oTop = $('#counter').offset().top - window.innerHeight;
  console.log('Counter top:', oTop, 'Scroll top:', $(window).scrollTop());
  // ... rest of code
});
```

### Issue: Images Not Displaying

**Symptoms:**
- Broken image icons
- 404 errors in console

**Solutions:**
1. Check file path (relative vs absolute)
2. Verify file exists
3. Check file name case sensitivity
4. Verify file extension

**Fix:**
```html
<!-- Use relative path from HTML file -->
<img src="resources1/images/logo.png" alt="Logo" />

<!-- Or absolute from root -->
<img src="/resources1/images/logo.png" alt="Logo" />
```

### Issue: Styles Not Applying

**Symptoms:**
- Changes not visible
- Old styles still showing

**Solutions:**
1. Hard refresh browser (Ctrl+F5)
2. Check CSS file is linked correctly
3. Verify CSS selector specificity
4. Check for syntax errors
5. Update version number

**Fix:**
```html
<!-- Update version to force refresh -->
<link rel="stylesheet" href="resources1/css/index.css?v=NEWVERSION" />
```

### Issue: JavaScript Errors

**Symptoms:**
- Features not working
- Console shows errors

**Solutions:**
1. Check script load order
2. Verify jQuery is loaded first
3. Check for syntax errors
4. Verify function names match
5. Check browser console for specific errors

**Common Errors:**
- `$ is not defined`: jQuery not loaded
- `Cannot read property of undefined`: Element doesn't exist
- `Uncaught TypeError`: Wrong function call

---

## ⚠️ Important Notes

### 1. Docker Development Environment
- **Primary Method**: Use Docker with nginx for local development
- **Volume Mounts**: Files are mounted, so changes reflect immediately (no rebuild needed)
- **Port**: Default is port 80, change if needed
- **Container Name**: `my_nginx_container` - remember this for Docker commands
- **Restart Container**: If issues occur, restart: `docker restart my_nginx_container`

### 2. File Paths
- **Always use relative paths** from HTML file location
- `resources1/` is the active resources folder
- `resources/` is legacy (avoid unless necessary)
- **Docker**: Ensure paths in Docker command match your actual file structure

### 2. Version Numbers
- Update version query strings when changing CSS/JS
- Format: `?v=26febv1` or `?v=1.0.0`
- Update in ALL files that reference the changed file

### 3. Browser Compatibility
- Test in Chrome, Firefox, Safari, Edge
- IE11 support may be limited
- Use modern browser features with fallbacks

### 4. Performance
- Optimize images before adding
- Use lazy loading for videos
- Minimize CSS/JS file sizes
- Use CDN for external libraries

### 5. Mobile Responsiveness
- Always test on mobile devices
- Use Bootstrap responsive classes
- Test at different screen sizes (320px, 768px, 1024px, 1920px)

### 6. SEO Considerations
- Use semantic HTML
- Include proper meta tags
- Use descriptive alt text for images
- Maintain heading hierarchy (h1, h2, h3)

### 7. Code Organization
- Keep related code together
- Comment complex logic
- Use consistent naming conventions
- Don't duplicate code (create reusable functions)

### 8. Testing Checklist
Before deploying changes:
- [ ] Test on desktop (Chrome, Firefox, Safari, Edge)
- [ ] Test on mobile (iOS Safari, Chrome Mobile)
- [ ] Test on tablet
- [ ] Check all links work
- [ ] Verify forms submit correctly
- [ ] Check console for errors
- [ ] Verify images load
- [ ] Test carousel functionality
- [ ] Verify responsive design
- [ ] Check page load speed

---

## 📚 Additional Resources

### External Documentation
- [Bootstrap 5 Docs](https://getbootstrap.com/docs/5.3/)
- [jQuery API](https://api.jquery.com/)
- [Slick Carousel](https://kenwheeler.github.io/slick/)
- [WOW.js](https://wowjs.uk/)
- [Docker Documentation](https://docs.docker.com/)
- [Nginx Documentation](https://nginx.org/en/docs/)

### Key Directories to Know
- `resources1/css/`: All stylesheets
- `resources1/js/`: All JavaScript files
- `resources1/images/`: All image assets
- `resources1/video/`: All video assets
- `header1.html`: Navigation component
- `footer1.html`: Footer component
---


### Docker Command Reference
```bash
# Start container
docker run -p 80:80 \
  -v "C:/Users/DELL/Documents/BDB Website/bdb_ai/bdb_ai/BDB.AI/WebContent:/usr/share/nginx/html" \
  -v "C:/Users/DELL/Documents/BDB Website/bdb_ai/bdb_ai/nginx.conf:/etc/nginx/nginx.conf" \
  --name my_nginx_container \
  -d nginx

# Stop container
docker stop my_nginx_container

# Start stopped container
docker start my_nginx_container

# Restart container
docker restart my_nginx_container

# View logs
docker logs my_nginx_container

# View logs (follow mode)
docker logs -f my_nginx_container

# Remove container
docker rm my_nginx_container

# Check running containers
docker ps

# Check all containers (including stopped)
docker ps -a

# Access container shell (for debugging)
docker exec -it my_nginx_container bash
```

---

<div style="background-color: #f5f5f5; border-radius: 8px; padding: 20px; margin: 30px 0; text-align: center;">
  <h3 style="margin-top: 0; color: #333;">📥 Download PDF Version</h3>
  <p style="margin: 10px 0;">Need a PDF copy? Click the button below!</p>
  <button onclick="window.print()" style="background-color: #2196f3; color: white; border: none; padding: 12px 30px; font-size: 16px; border-radius: 5px; cursor: pointer; font-weight: bold; box-shadow: 0 2px 4px rgba(0,0,0,0.2); margin: 10px;">
    🖨️ Download as PDF
  </button>
  <p style="margin: 10px 0; font-size: 14px; color: #666;">
    <strong>Tip:</strong> In the print dialog, select "Save as PDF" as destination, then click Save.
  </p>
</div>

**Last Updated**: 2025
