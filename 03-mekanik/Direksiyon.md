# Direksiyon Sistemi

#mekanik #direksiyon #ackermann #geometry

## Genel Bakış

Efficiency Challenge için hafif, responsive direksiyon sistemi. Ackermann geometry ile optimum turning, minimal drag.

> [!note] Hedefler
> - **Tip:** Rack and pinion (önerilen)
> - **Steering ratio:** 12:1 - 15:1
> - **Lock-to-lock:** 3.0 - 3.5 turns
> - **Ağırlık:** <5kg total sistem

## Steering Type Options

### 1. Rack and Pinion (Recommended)
```mermaid
graph TD
    A[Rack & Pinion] --> B[Pros]
    A --> C[Cons]
    
    B --> B1[Precise control]
    B --> B2[Direct feel]
    B --> B3[Compact design]
    B --> B4[Low friction]
    
    C --> C1[More complex]
    C --> C2[Higher cost]
```

**Specs:**
- **Rack travel:** 80-120mm total
- **Pinion diameter:** 25-35mm
- **Material:** Steel rack, aluminum housing
- **Maliyet:** ₺1500-3000

### 2. Worm and Sector
```mermaid
graph TD
    A[Worm & Sector] --> B[Pros]
    A --> C[Cons]
    
    B --> B1[Simple design]
    B --> B2[High reduction ratio]
    B --> B3[Self-locking]
    
    C --> C1[Higher friction]
    C --> C2[Less precise]
    C --> C3[More play/backlash]
```

**Use case:** Budget option, easier manufacturing

### 3. Direct Linkage
```mermaid
graph TD
    A[Direct Linkage] --> B[Pros]
    A --> C[Cons]
    
    B --> B1[Lightest option]
    B --> B2[Minimum friction]
    B --> B3[Very simple]
    
    C --> C1[High steering effort]
    C --> C2[Poor for highway use]
    C --> C3[Bump steer issues]
```

**Use case:** Ultra-lightweight builds only

## Ackermann Geometry

### Geometry Calculation

```mermaid
graph LR
    A[Wheelbase: L] --> B[Track Width: T]
    B --> C[Ackermann Angle]
    C --> D[Inner Wheel: δᵢ]
    C --> E[Outer Wheel: δₒ]
    
    F[Formula] --> G[cot δₒ - cot δᵢ = T/L]
```

**Given:**
- **Wheelbase (L):** 2000mm
- **Track width (T):** 1300mm
- **Ackermann factor:** T/L = 1300/2000 = 0.65

**Tie rod geometry:**
- **Tie rod length:** Calculate for Ackermann
- **Pickup points:** Behind axle centerline
- **Angle:** ~5-8° from centerline at straight ahead

### Practical Implementation

```mermaid
graph TD
    A[Straight Ahead] --> B[0° both wheels]
    C[Left Turn 30°] --> D[Inner: 32.5°]
    C --> E[Outer: 27.8°]
    F[Right Turn 30°] --> G[Inner: 32.5°]
    F --> H[Outer: 27.8°]
```

**Verification:**
- Measure actual angles with protractor
- Check tire scrub marks in testing
- Adjust tie rod lengths if needed

## Steering Ratio

### Calculation
```
Steering Ratio = (Steering Wheel Rotation) / (Front Wheel Rotation)
```

**Target values:**
- **Racing:** 12:1 - 14:1 (quick response)
- **Comfort:** 15:1 - 17:1 (easier at parking speeds)
- **Our target:** 13:1 - 15:1

### Ratio Factors
```mermaid
graph LR
    A[Steering Wheel] --> B[Column Ratio: 1:1]
    B --> C[Pinion Gear]
    C --> D[Rack Travel]
    D --> E[Tie Rod Leverage]
    E --> F[Wheel Angle]
```

**Components:**
- **Steering wheel:** 300-350mm diameter
- **Pinion gear:** 12-16 teeth (smaller = quicker)
- **Rack pitch:** 2-3mm per tooth
- **Tie rod geometry:** Affects leverage ratio

## Components

### Steering Wheel
```mermaid
graph TD
    A[Steering Wheel] --> B[Diameter: 300mm]
    A --> C[Material: Aluminum + foam grip]
    A --> D[Weight: <1kg]
    A --> E[Quick release optional]
```

**Specs:**
- **Diameter:** 300-320mm (compromise size/leverage)
- **Material:** Aluminum rim + urethane/foam grip
- **Hub:** 3-bolt or 6-bolt pattern
- **Weight:** <1kg
- **Features:** Optional quick-release for driver access

### Steering Column
- **Type:** Adjustable tilt (optional)
- **Material:** Steel tube (seamless)
- **Diameter:** 25-30mm OD
- **Length:** 400-600mm
- **Bearings:** Sealed ball bearings top/bottom
- **U-joints:** Double Cardan joint for angle changes

### Tie Rods
```mermaid
graph LR
    A[Tie Rod Ends] --> B[Rod End Bearings]
    B --> C[M10 or M12 thread]
    
    D[Tie Rod] --> E[Steel tube]
    E --> F[Adjustable length]
    F --> G[Left/right hand thread]
```

**Specifications:**
- **Rod ends:** M10 or M12 male/female threads
- **Material:** Steel, chromoly preferred
- **Adjustment:** Left-hand and right-hand threads
- **Length:** Calculate for geometry + adjustment range

### Ball Joints
- **Type:** Maintenance-free sealed units
- **Load rating:** Min 500kg radial
- **Material:** Steel ball, polymer socket
- **Grease:** Sealed for life
- **Mounting:** Taper fit to knuckle

## Build Checklist

### Design Phase
- [ ] Wheelbase and track width finalized
- [ ] Ackermann geometry calculated
- [ ] Steering ratio determined
- [ ] Rack and pinion sizing completed
- [ ] Component selection finalized
- [ ] Weight budget allocated (<5kg)

### Procurement
- [ ] Rack and pinion unit sourced
- [ ] Steering wheel selected
- [ ] Steering column components
- [ ] Tie rod ends (4x minimum)
- [ ] Tie rod tubes (2x adjustable)
- [ ] Ball joints for knuckles
- [ ] Mounting brackets material
- [ ] Fasteners and hardware

### Manufacturing
- [ ] Rack mounting brackets fabricated
- [ ] Column mounting brackets made
- [ ] Column tube cut to length
- [ ] U-joint connections machined
- [ ] Tie rod tubes cut and threaded

### Assembly
- [ ] Rack mounted to chassis
- [ ] Column installed and aligned
- [ ] Pinion gear engagement verified
- [ ] Tie rods assembled and adjusted
- [ ] Ball joints installed to knuckles
- [ ] Steering wheel mounted

### Alignment & Geometry
- [ ] Front wheels straight ahead verified
- [ ] Steering wheel centered
- [ ] Toe adjustment completed
- [ ] Ackermann angles measured and verified
- [ ] Lock-to-lock travel checked
- [ ] Bump steer test completed

### Testing
- [ ] Steering effort measurement
- [ ] Free play/backlash check (<5°)
- [ ] Full lock testing (no binding)
- [ ] Dynamic test (low speed maneuvering)
- [ ] High-speed stability verification
- [ ] Emergency maneuver testing

### Competition Prep
- [ ] All fasteners double-checked and torqued
- [ ] Tie rod adjustment locks secured
- [ ] Grease all serviceable joints
- [ ] Spare tie rod ends packed
- [ ] Alignment tools ready for setup
- [ ] Quick-release steering wheel practice

### Performance Optimization
- [ ] Steering effort optimization
- [ ] Geometry fine-tuning based on testing
- [ ] Friction reduction (bearings, joints)
- [ ] Weight reduction review
- [ ] Driver feedback incorporation

---

**Related:** [[Sasi]] | [[Suspansiyon]] | [[Tekerlekler]]
**Tags:** #mekanik #direksiyon #ackermann #geometry
**Owner:** Teknik Çizim team
**Dependencies:** Chassis design, front suspension layout
**Status:** Design phase
**Last updated:** {{date}}