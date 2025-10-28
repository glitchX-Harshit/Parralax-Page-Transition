# Complete Guide: Building Parallax Scroll Animations with GSAP

## Table of Contents
1. Core Concepts
2. HTML Structure
3. CSS Positioning Strategy
4. GSAP Fundamentals
5. ScrollTrigger Deep Dive
6. Step-by-Step Breakdown
7. Animation Techniques

---

## 1. CORE CONCEPTS

### What is Parallax Scrolling?
Parallax scrolling creates depth by moving elements at different speeds as you scroll. Think of it like looking out a car window - close objects move faster than distant mountains.

### The "Slide Over" Effect
Instead of pages scrolling normally, each page:
- Stays **fixed** in position
- The next page slides UP from the bottom
- Creates a layered, stacking effect

### Key Technologies
- **GSAP (GreenSock Animation Platform)**: Animation engine
- **ScrollTrigger**: GSAP plugin that links animations to scroll position
- **CSS Fixed Positioning**: Keeps pages in place
- **Z-index Layering**: Controls which page appears on top

---

## 2. HTML STRUCTURE

```html
<div class="container">
    <!-- Container provides scroll height -->
    
    <div class="panel" id="page1">
        <!-- First page content -->
    </div>
    
    <div class="panel" id="page2">
        <!-- Second page content -->
    </div>
    
    <!-- More pages... -->
</div>
```

### Why This Structure?

**Container (`height: 500vh`)**
- Creates scrollable space (5x viewport height for 5 pages)
- Each page gets ~100vh of scroll space
- Without this, there'd be nothing to scroll!

**Panels (`.panel`)**
- Each is `position: fixed` - doesn't move with scroll
- `height: 100vh` - fills entire viewport
- Stacked on top of each other using z-index

---

## 3. CSS POSITIONING STRATEGY

### The Magic of Fixed Positioning

```css
.container {
    position: relative;
    height: 500vh; /* 5 pages × 100vh each */
}

.panel {
    position: fixed;  /* Key! Doesn't scroll naturally */
    top: 0;
    left: 0;
    height: 100vh;
    width: 100%;
}
```

### Z-index Layering

```css
#page1 { z-index: 1; }  /* Bottom layer */
#page2 { z-index: 2; }  /* On top of page1 */
#page3 { z-index: 3; }  /* On top of page2 */
#page4 { z-index: 4; }
#page5 { z-index: 5; }  /* Top layer */
```

**Why it matters:**
- Page 5 is naturally on top of all others
- When Page 2 slides up, it covers Page 1
- Z-index determines stacking order

### Initial Panel Positioning

All panels except the first start **below** the viewport:

```css
/* In JavaScript, initial state */
yPercent: 100  /* 100% below viewport */
```

When scrolling triggers, they slide to:
```css
yPercent: 0    /* Normal position */
```

---

## 4. GSAP FUNDAMENTALS

### What is GSAP?

GSAP animates CSS properties over time. Instead of:
```css
transition: transform 0.3s;
```

You get precise control:
```javascript
gsap.to(element, {
    x: 100,           // Move 100px right
    duration: 1,      // Over 1 second
    ease: "power2.out" // With easing
});
```

### Core GSAP Methods

**gsap.to()** - Animate FROM current state TO target
```javascript
gsap.to("#box", { x: 100 }); // Move to x: 100
```

**gsap.from()** - Animate FROM specified state TO current
```javascript
gsap.from("#box", { opacity: 0 }); // Fade in
```

**gsap.fromTo()** - Specify both start and end
```javascript
gsap.fromTo("#box", 
    { x: 0 },      // Start here
    { x: 100 }     // End here
);
```

**gsap.timeline()** - Chain multiple animations
```javascript
const tl = gsap.timeline();
tl.to("#box1", { x: 100 })
  .to("#box2", { y: 50 });  // Runs after box1
```

### GSAP Properties

```javascript
{
    x: 100,           // translateX(100px)
    y: 50,            // translateY(50px)
    xPercent: 100,    // translateX(100%)
    yPercent: 100,    // translateY(100%)
    rotation: 45,     // rotate(45deg)
    scale: 1.5,       // scale(1.5)
    opacity: 0,       // opacity: 0
    duration: 1,      // How long (seconds)
    ease: "power2",   // Easing function
    delay: 0.5        // Wait before starting
}
```

---

## 5. SCROLLTRIGGER DEEP DIVE

### What is ScrollTrigger?

ScrollTrigger links animations to scroll position. As you scroll, it:
1. Monitors scroll position
2. Triggers animations at specific points
3. Can "scrub" animations (link to scroll progress)

### Basic ScrollTrigger Setup

```javascript
gsap.registerPlugin(ScrollTrigger); // Enable plugin

gsap.to("#element", {
    x: 100,
    scrollTrigger: {
        trigger: "#element",  // Element to watch
        start: "top center",  // When to start
        end: "bottom center", // When to end
        scrub: true           // Link to scroll
    }
});
```

### ScrollTrigger Properties Explained

#### **trigger**
```javascript
trigger: ".container"
```
- The element ScrollTrigger watches
- When THIS element enters viewport, animation can start

#### **start**
```javascript
start: "top top"
```
Format: `"trigger_position viewport_position"`

Examples:
- `"top top"` - Trigger's top hits viewport top
- `"top center"` - Trigger's top hits viewport center
- `"20% top"` - When 20% of trigger is scrolled past viewport top

#### **end**
```javascript
end: "bottom top"
```
Same format as start - defines when animation completes

Examples:
- `"bottom bottom"` - Trigger's bottom hits viewport bottom
- `"+=500"` - 500px after start position
- `"40% top"` - When scrolled to 40% of trigger

#### **scrub**
```javascript
scrub: true   // Link to scroll (instant)
scrub: 1      // Link to scroll (1 second smooth delay)
```
- `false`: Animation plays once when triggered
- `true`: Animation progress = scroll progress (instant)
- `number`: Smoothed scroll link (better feel)

#### **pin**
```javascript
pin: true
```
- Pins (fixes) the trigger element during animation
- Element stays in place while animation plays
- **We don't use this** because our elements are already fixed

#### **markers** (for debugging)
```javascript
markers: true
```
Shows visual markers for start/end points - great for development!

---

## 6. STEP-BY-STEP BREAKDOWN

### Step 1: Create the Container

```html
<div class="container"></div>
```

```css
.container {
    height: 500vh; /* 5 pages = 500vh */
}
```

**Why 500vh?**
- 5 pages × 100vh per page = 500vh
- Creates scroll space for all transitions
- Each page gets ~100vh of scroll to animate

### Step 2: Add Fixed Panels

```html
<div class="panel" id="page1">Content</div>
<div class="panel" id="page2">Content</div>
```

```css
.panel {
    position: fixed;
    top: 0;
    left: 0;
    height: 100vh;
    width: 100%;
}

#page1 { z-index: 1; }
#page2 { z-index: 2; }
```

**Result:**
- All panels overlap at top-left
- Page 2 is above Page 1 (higher z-index)
- Nothing moves yet - all fixed

### Step 3: Position Panels Below Viewport

```javascript
// Page 2 starts below viewport
gsap.set("#page2", { yPercent: 100 });
```

Or set initial state in animation:
```javascript
gsap.fromTo("#page2",
    { yPercent: 100 },  // Start: below viewport
    { yPercent: 0 }     // End: normal position
);
```

**Why yPercent instead of y?**
- `yPercent: 100` = move down 100% of element's height
- Always moves exactly one full screen
- Works on any screen size (responsive)
- `y: 1000` would be fixed pixels (breaks on mobile)

### Step 4: Create Slide-Up Animation

```javascript
gsap.timeline({
    scrollTrigger: {
        trigger: '.container',
        start: 'top top',        // Container top hits viewport top
        end: '20% top',          // When scrolled 20% of container
        scrub: 1,                // Smooth scroll-linked
    }
})
.fromTo('#page2', 
    { yPercent: 100 },          // Start below
    { yPercent: 0 }             // Slide to normal position
);
```

**What happens:**
1. User scrolls
2. When container reaches top, animation starts
3. As user scrolls down 20% of container height:
   - Page 2 slides from `yPercent: 100` → `yPercent: 0`
4. `scrub: 1` makes it smooth and scroll-linked

### Step 5: Repeat for Other Pages

```javascript
// Page 3 slides over Page 2
gsap.timeline({
    scrollTrigger: {
        trigger: '.container',
        start: '20% top',   // After page 2 finishes
        end: '40% top',     // Next 20% of scroll
        scrub: 1,
    }
})
.fromTo('#page3', 
    { yPercent: 100 }, 
    { yPercent: 0 }
);

// Page 4 slides over Page 3
gsap.timeline({
    scrollTrigger: {
        trigger: '.container',
        start: '40% top',   // After page 3
        end: '60% top',     
        scrub: 1,
    }
})
.fromTo('#page4', 
    { yPercent: 100 }, 
    { yPercent: 0 }
);
```

**The Pattern:**
- Each page has its own scroll range
- Page 2: 0% → 20%
- Page 3: 20% → 40%
- Page 4: 40% → 60%
- Page 5: 60% → 80%

---

## 7. ANIMATION TECHNIQUES

### Adding Parallax to Background Elements

```javascript
gsap.to('#page1 .shape1', {
    x: -100,                    // Move left
    y: -100,                    // Move up
    scale: 1.5,                 // Grow bigger
    scrollTrigger: {
        trigger: '.container',
        start: 'top top',
        end: '20% top',         // Same as page transition
        scrub: 1
    }
});
```

**Why it works:**
- Runs during same scroll range as page slide
- Background moves at different speed than page
- Creates depth perception

### Fading Out Content

```javascript
gsap.to('#page1 .content', {
    opacity: 0,                 // Fade out
    y: -50,                     // Move up slightly
    scrollTrigger: {
        trigger: '.container',
        start: 'top top',
        end: '20% top',
        scrub: 1
    }
});
```

**Effect:**
- Text fades as page transitions
- Prevents text overlap between pages
- Creates smooth visual transition

### Rotating Elements

```javascript
gsap.to('#page3 .box1', {
    x: -200,
    y: -200,
    rotation: 180,              // Spin 180 degrees
    scrollTrigger: {
        trigger: '.container',
        start: '40% top',
        end: '60% top',
        scrub: 1
    }
});
```

### Progress Bar

```javascript
gsap.to('#progressBar', {
    width: '100%',              // Grow to full width
    ease: 'none',               // Linear progress
    scrollTrigger: {
        trigger: 'body',        // Watch entire page
        start: 'top top',       
        end: 'bottom bottom',   // Full page height
        scrub: 0.3              // Slight smoothing
    }
});
```

---

## 8. COMPLETE IMPLEMENTATION FLOW

### Initialization
```javascript
// 1. Register plugin
gsap.registerPlugin(ScrollTrigger);

// 2. Set initial positions (optional if using fromTo)
gsap.set("#page2", { yPercent: 100 });
gsap.set("#page3", { yPercent: 100 });
// etc...
```

### Animation Structure
```javascript
// 3. Create animations for each page transition
// Page 2 slides in
gsap.timeline({
    scrollTrigger: {
        trigger: '.container',
        start: 'top top',
        end: '20% top',
        scrub: 1,
    }
})
.fromTo('#page2', { yPercent: 100 }, { yPercent: 0 });

// 4. Add parallax effects during same scroll range
gsap.to('#page1 .background', {
    scale: 1.5,
    opacity: 0,
    scrollTrigger: {
        trigger: '.container',
        start: 'top top',      // Same as page 2
        end: '20% top',        // Same as page 2
        scrub: 1
    }
});
```

---

## 9. KEY CONCEPTS SUMMARY

### CSS Foundation
1. **Fixed positioning** - Panels don't scroll naturally
2. **Z-index layering** - Controls stacking order
3. **Container height** - Creates scroll space
4. **Transform properties** - Smooth animations (GPU accelerated)

### GSAP Core
1. **gsap.to()** - Animate to target state
2. **gsap.fromTo()** - Define start and end
3. **Timeline** - Chain animations together
4. **Properties** - x, y, rotation, scale, opacity, etc.

### ScrollTrigger Magic
1. **trigger** - Element to watch
2. **start/end** - When animation begins/ends
3. **scrub** - Link animation to scroll position
4. **Scroll ranges** - Divide container into sections

### The Slide-Over Effect
1. All panels fixed and stacked
2. Higher z-index on top
3. Panels start below viewport (yPercent: 100)
4. Scroll triggers slide to normal position (yPercent: 0)
5. Previous panel stays fixed, new panel slides over

---

## 10. DEBUGGING TIPS

### Add Markers
```javascript
scrollTrigger: {
    markers: true,  // Shows start/end points
    id: "page2"     // Label for markers
}
```

### Console Logging
```javascript
scrollTrigger: {
    onEnter: () => console.log("Entered!"),
    onLeave: () => console.log("Left!"),
    onUpdate: (self) => console.log(self.progress)
}
```

### Check ScrollTrigger State
```javascript
ScrollTrigger.getAll(); // All triggers
ScrollTrigger.refresh(); // Recalculate positions
```

---

## 11. COMMON MISTAKES TO AVOID

1. **Forgetting to register ScrollTrigger**
   ```javascript
   gsap.registerPlugin(ScrollTrigger); // Essential!
   ```

2. **Wrong trigger element**
   - Use `.container` for slide animations
   - Don't use the moving panel itself

3. **Incorrect start/end values**
   - Must be strings: `"top top"` not `top top`
   - Check percentage values match scroll ranges

4. **Missing container height**
   - Without height, nothing to scroll
   - Calculate: number_of_pages × 100vh

5. **Z-index conflicts**
   - Ensure each page has unique z-index
   - Higher number = on top

6. **Using px instead of percent**
   - `yPercent: 100` ✓ (responsive)
   - `y: 1000` ✗ (breaks on mobile)

---

## 12. ADVANCED TECHNIQUES

### Easing Functions
```javascript
ease: "power2.inOut"    // Slow start and end
ease: "elastic.out"     // Bouncy
ease: "steps(5)"        // Stepped animation
ease: "none"            // Linear
```

### Custom ScrollTrigger Callbacks
```javascript
scrollTrigger: {
    onEnter: () => console.log("Page entered"),
    onLeave: () => console.log("Page left"),
    onEnterBack: () => console.log("Scrolling back up"),
    onLeaveBack: () => console.log("Leaving backwards")
}
```

### Responsive Animations
```javascript
ScrollTrigger.matchMedia({
    "(min-width: 800px)": function() {
        // Desktop animations
    },
    "(max-width: 799px)": function() {
        // Mobile animations
    }
});
```

---

## 13. PRACTICE EXERCISE

Try building this step by step:

1. Create 3 pages with fixed positioning
2. Set up container with 300vh height
3. Add z-index: 1, 2, 3
4. Animate page 2 sliding from yPercent: 100 to 0
5. Animate page 3 sliding from yPercent: 100 to 0
6. Add a background shape that scales during transition
7. Add content fade out effect
8. Add progress bar

---

## FINAL NOTES

**The Magic Formula:**
- Fixed panels + z-index stacking
- Transform (yPercent) for sliding
- ScrollTrigger + scrub for scroll-linking
- Parallax on background elements for depth

**Remember:**
- GSAP handles the animations
- ScrollTrigger links them to scroll
- CSS positions everything
- You control timing with start/end values

**Practice makes perfect!** Start simple, add complexity gradually.
