---
name: Care & Clarity
colors:
  surface: '#f8f9ff'
  surface-dim: '#cbdbf5'
  surface-bright: '#f8f9ff'
  surface-container-lowest: '#ffffff'
  surface-container-low: '#eff4ff'
  surface-container: '#e5eeff'
  surface-container-high: '#dce9ff'
  surface-container-highest: '#d3e4fe'
  on-surface: '#0b1c30'
  on-surface-variant: '#434655'
  inverse-surface: '#213145'
  inverse-on-surface: '#eaf1ff'
  outline: '#737686'
  outline-variant: '#c3c6d7'
  surface-tint: '#0053db'
  primary: '#004ac6'
  on-primary: '#ffffff'
  primary-container: '#2563eb'
  on-primary-container: '#eeefff'
  inverse-primary: '#b4c5ff'
  secondary: '#006a61'
  on-secondary: '#ffffff'
  secondary-container: '#86f2e4'
  on-secondary-container: '#006f66'
  tertiary: '#784b00'
  on-tertiary: '#ffffff'
  tertiary-container: '#996100'
  on-tertiary-container: '#ffeedd'
  error: '#ba1a1a'
  on-error: '#ffffff'
  error-container: '#ffdad6'
  on-error-container: '#93000a'
  primary-fixed: '#dbe1ff'
  primary-fixed-dim: '#b4c5ff'
  on-primary-fixed: '#00174b'
  on-primary-fixed-variant: '#003ea8'
  secondary-fixed: '#89f5e7'
  secondary-fixed-dim: '#6bd8cb'
  on-secondary-fixed: '#00201d'
  on-secondary-fixed-variant: '#005049'
  tertiary-fixed: '#ffddb8'
  tertiary-fixed-dim: '#ffb95f'
  on-tertiary-fixed: '#2a1700'
  on-tertiary-fixed-variant: '#653e00'
  background: '#f8f9ff'
  on-background: '#0b1c30'
  surface-variant: '#d3e4fe'
typography:
  headline-xl:
    fontFamily: Pretendard
    fontSize: 48px
    fontWeight: '700'
    lineHeight: 56px
    letterSpacing: -0.02em
  headline-lg:
    fontFamily: Pretendard
    fontSize: 32px
    fontWeight: '700'
    lineHeight: 40px
    letterSpacing: -0.01em
  headline-lg-mobile:
    fontFamily: Pretendard
    fontSize: 28px
    fontWeight: '700'
    lineHeight: 36px
    letterSpacing: -0.01em
  headline-md:
    fontFamily: Pretendard
    fontSize: 24px
    fontWeight: '600'
    lineHeight: 32px
  body-lg:
    fontFamily: Pretendard
    fontSize: 18px
    fontWeight: '400'
    lineHeight: 28px
  body-md:
    fontFamily: Pretendard
    fontSize: 16px
    fontWeight: '400'
    lineHeight: 24px
  body-sm:
    fontFamily: Pretendard
    fontSize: 14px
    fontWeight: '400'
    lineHeight: 20px
  label-md:
    fontFamily: Pretendard
    fontSize: 14px
    fontWeight: '600'
    lineHeight: 20px
    letterSpacing: 0.05em
  label-sm:
    fontFamily: Pretendard
    fontSize: 12px
    fontWeight: '500'
    lineHeight: 16px
rounded:
  sm: 0.25rem
  DEFAULT: 0.5rem
  md: 0.75rem
  lg: 1rem
  xl: 1.5rem
  full: 9999px
spacing:
  base: 8px
  xs: 4px
  sm: 12px
  md: 16px
  lg: 24px
  xl: 40px
  gutter: 24px
  margin-mobile: 16px
  margin-desktop: 48px
---

## Brand & Style
The design system is built for the health and wellness sector, focusing on accessibility, trust, and a sense of calm. The brand personality is empathetic yet professional, aiming to reduce cognitive load for users who may be in stressful situations.

The design style is **Corporate Modern with a Soft Touch**. It leverages ample whitespace and a systematic approach to hierarchy while incorporating subtle, friendly elements like soft shadows and rounded corners to feel approachable rather than clinical.

## Colors
The palette is rooted in a trustworthy **Primary Blue** (#2563EB) to signal reliability. The **Secondary Teal** (#0D9488) is used for health-specific actions and success states, providing a calming alternative to the primary blue. **Tertiary Amber** (#F59E0B) is reserved for warnings and highlights that require attention without inducing panic.

The **Neutral** scale uses cool grays to maintain a clean, modern aesthetic. Backgrounds should primarily use off-whites and very light gray tints to reduce eye strain.

## Typography
This design system utilizes **Pretendard** across all levels to ensure a clean, highly legible, and modern feel. As a variable font, it provides exceptional clarity across different weights and screen densities.

Headlines use a bolder weight and tighter letter spacing to create a strong visual anchor. Body text is set with generous line heights to enhance readability for long-form health information. Labels use a slightly heavier weight and increased tracking to remain distinct at smaller sizes.

## Layout & Spacing
The layout follows a **Fluid Grid** model with a 12-column structure for desktop and a 4-column structure for mobile. 

The spacing rhythm is based on an **8px linear scale**. Use `md` (16px) for standard internal padding of cards and `lg` (24px) for vertical spacing between sections. Margins should expand on larger screens to allow the content to breathe, preventing the UI from feeling cramped.

## Elevation & Depth
Hierarchy is established through **Tonal Layers** and **Ambient Shadows**. 

The base background is the lowest level. Content sits on "Surface" containers which use a subtle white or high-contrast background. For interactive elements like cards or modals, use soft, diffused shadows with a low opacity (typically 4-8%) and a slight blue tint derived from the primary color to maintain a cohesive atmosphere. Avoid harsh, black shadows to keep the interface light and welcoming.

## Shapes
The shape language uses a **Rounded** approach (Level 2). This eliminates sharp corners that can feel aggressive, replacing them with 0.5rem (8px) radiuses for standard components. 

Large containers like cards should use `rounded-lg` (16px) to emphasize the soft, protective nature of the design. Small utility elements like checkboxes use the base 4px roundedness to remain crisp while still appearing friendly.

## Components
- **Buttons**: Primary buttons feature a solid primary color fill with `body-md` bold white text. Secondary buttons use an outline style with a 1px border in the primary color.
- **Input Fields**: Use a light gray border (#E2E8F0) that transitions to the primary blue on focus. Include clear labels using `label-sm` above the field.
- **Cards**: Surface containers with `rounded-lg` corners, a 1px soft border, and a subtle ambient shadow.
- **Chips**: Used for categories or filters, these should have a pill-shape (fully rounded) and use light tints of the secondary or primary colors with high-contrast text.
- **Lists**: Items are separated by a 1px subtle divider; use `body-md` for the title and `body-sm` for supporting metadata to create clear information hierarchy.
- **Feedback Indicators**: Use the tertiary amber for "In Progress" states and the secondary teal for "Completed" or "Healthy" indicators.