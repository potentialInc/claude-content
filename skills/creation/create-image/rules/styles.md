---
name: image-styles
description: Background styles and presets for image generation
metadata:
  tags: gemini, style, background, white, transparent, custom
---

# Image Styles

Apply different background styles and visual presets to generated images.

## Background Styles

### White Background

Generate images with a pure white background, ideal for product photos and e-commerce:

```typescript
async function generateWithWhiteBackground(
  prompt: string,
  outputPath: string
) {
  const enhancedPrompt = `${prompt}, on a pure white background (#FFFFFF), clean and isolated, professional product photography style, no shadows`;
  return generateImage(enhancedPrompt, outputPath);
}

// Usage
generateWithWhiteBackground(
  "A red apple",
  "public/images/generated/apple_white_bg.png"
);
```

### Transparent Background

Generate images with transparent backgrounds (alpha channel):

```typescript
async function generateWithTransparentBackground(
  prompt: string,
  outputPath: string
) {
  // Request transparent/isolated subject with strong emphasis
  const enhancedPrompt = `${prompt}, isolated character on pure transparent background, PNG with alpha channel, no background at all, subject only, clean cutout, floating in empty space, absolutely no ground or shadows or environment`;
  return generateImage(enhancedPrompt, outputPath);
}

// Usage
generateWithTransparentBackground(
  "A fox mascot logo",
  "public/images/generated/fox_logo_transparent.png"
);
```

**⚠️ Important: Gemini Transparency Limitations**

Gemini AI may not always produce true transparent backgrounds even with explicit prompts. To ensure transparency:

1. **Post-process with rembg** (Recommended):
   ```bash
   # Install rembg
   pip install rembg

   # Remove background from single image
   rembg i input.png output.png

   # Process entire directory
   rembg p input_folder/ output_folder/
   ```

2. **Use white background as alternative**: For light-themed videos/designs, white backgrounds often work better than attempting transparency.

3. **Combine approaches**: Generate with `--style transparent`, then post-process with `rembg` for guaranteed results.

### Custom Background Color

Generate with a specific background color:

```typescript
async function generateWithCustomBackground(
  prompt: string,
  backgroundColor: string, // Hex color like "#f0f0f0"
  outputPath: string
) {
  const colorName = hexToColorName(backgroundColor);
  const enhancedPrompt = `${prompt}, on a solid ${colorName} background (${backgroundColor}), clean background, no patterns`;
  return generateImage(enhancedPrompt, outputPath);
}

function hexToColorName(hex: string): string {
  const colors: Record<string, string> = {
    "#ffffff": "white",
    "#000000": "black",
    "#f0f0f0": "light gray",
    "#e0e0e0": "silver",
    "#ffd700": "gold",
    "#87ceeb": "sky blue",
    "#90ee90": "light green",
    "#ffb6c1": "light pink",
  };
  return colors[hex.toLowerCase()] || `${hex} colored`;
}

// Usage
generateWithCustomBackground(
  "A sleek smartwatch",
  "#87ceeb",
  "public/images/generated/watch_blue_bg.png"
);
```

## Style Presets

### Product Photography

```typescript
const productPhotoPreset = {
  name: "product",
  modifiers: [
    "professional product photography",
    "studio lighting",
    "high detail",
    "sharp focus",
    "clean presentation",
  ],
  defaultBackground: "white",
};

async function generateProductPhoto(
  productDescription: string,
  outputPath: string,
  options?: { background?: string }
) {
  const bg = options?.background || productPhotoPreset.defaultBackground;
  const modifiers = productPhotoPreset.modifiers.join(", ");
  const prompt = `${productDescription}, ${modifiers}, on ${bg} background`;
  return generateImage(prompt, outputPath);
}

// Usage
generateProductPhoto(
  "Wireless bluetooth headphones",
  "public/images/generated/headphones_product.png"
);
```

### Logo Design

```typescript
const logoPreset = {
  name: "logo",
  modifiers: [
    "logo design",
    "flat design",
    "minimalist",
    "scalable vector style",
    "clean lines",
  ],
  defaultBackground: "transparent",
};

async function generateLogo(
  logoDescription: string,
  outputPath: string,
  options?: { style?: "flat" | "3d" | "vintage" }
) {
  const style = options?.style || "flat";
  const styleModifiers: Record<string, string> = {
    flat: "flat design, 2D, simple shapes",
    "3d": "3D rendered, subtle shadows, depth",
    vintage: "vintage style, retro, badge design",
  };

  const prompt = `${logoDescription}, ${logoPreset.modifiers.join(", ")}, ${styleModifiers[style]}, isolated on transparent background`;
  return generateImage(prompt, outputPath);
}

// Usage
generateLogo(
  "A mountain peak with sun",
  "public/images/generated/mountain_logo.png",
  { style: "flat" }
);
```

### Social Media Graphics

```typescript
const socialMediaPresets = {
  instagram: {
    size: "1080x1080",
    style: "vibrant colors, eye-catching, modern design",
  },
  facebook: {
    size: "1200x630",
    style: "clean layout, professional, engaging",
  },
  twitter: {
    size: "1600x900",
    style: "bold text-friendly, high contrast",
  },
};

async function generateSocialGraphic(
  content: string,
  platform: keyof typeof socialMediaPresets,
  outputPath: string
) {
  const preset = socialMediaPresets[platform];
  const prompt = `${content}, ${preset.style}, ${preset.size} aspect ratio`;
  return generateImage(prompt, outputPath);
}
```

### Illustration Styles

```typescript
const illustrationStyles = {
  watercolor: {
    modifiers: "watercolor painting, soft edges, flowing colors, artistic",
    background: "textured paper",
  },
  digitalArt: {
    modifiers: "digital art, clean lines, vibrant colors, polished",
    background: "gradient",
  },
  sketch: {
    modifiers: "pencil sketch, hand-drawn, graphite, artistic lines",
    background: "white paper",
  },
  anime: {
    modifiers: "anime style, cel shaded, Japanese animation aesthetic",
    background: "clean",
  },
  vintage: {
    modifiers: "vintage illustration, retro, aged paper texture",
    background: "cream paper",
  },
  lineart: {
    modifiers: "line art, black ink, minimal shading, clean strokes",
    background: "white",
  },
};

async function generateIllustration(
  subject: string,
  style: keyof typeof illustrationStyles,
  outputPath: string
) {
  const preset = illustrationStyles[style];
  const prompt = `${subject}, ${preset.modifiers}, on ${preset.background} background`;
  return generateImage(prompt, outputPath);
}

// Usage
generateIllustration(
  "A cozy coffee shop scene",
  "watercolor",
  "public/images/generated/coffee_shop.png"
);
```

## Style Configuration Interface

```typescript
interface StyleConfig {
  background: {
    type: "white" | "transparent" | "custom" | "auto";
    color?: string; // Hex color for custom type
  };
  lighting?: "studio" | "natural" | "dramatic" | "soft";
  quality?: "standard" | "high" | "ultra";
  composition?: "centered" | "rule-of-thirds" | "dynamic";
}

async function generateWithStyle(
  prompt: string,
  style: StyleConfig,
  outputPath: string
) {
  const parts: string[] = [prompt];

  // Background
  switch (style.background.type) {
    case "white":
      parts.push("on pure white background");
      break;
    case "transparent":
      parts.push("isolated on transparent background, no background");
      break;
    case "custom":
      parts.push(`on solid ${style.background.color} background`);
      break;
    // "auto" lets AI decide
  }

  // Lighting
  const lightingModifiers: Record<string, string> = {
    studio: "professional studio lighting, even illumination",
    natural: "natural lighting, soft shadows",
    dramatic: "dramatic lighting, strong contrast, moody",
    soft: "soft diffused lighting, gentle shadows",
  };
  if (style.lighting) {
    parts.push(lightingModifiers[style.lighting]);
  }

  // Quality
  const qualityModifiers: Record<string, string> = {
    standard: "good quality",
    high: "high quality, detailed",
    ultra: "ultra high quality, 8K, extremely detailed, sharp",
  };
  if (style.quality) {
    parts.push(qualityModifiers[style.quality]);
  }

  // Composition
  const compositionModifiers: Record<string, string> = {
    centered: "centered composition",
    "rule-of-thirds": "rule of thirds composition",
    dynamic: "dynamic composition, interesting angle",
  };
  if (style.composition) {
    parts.push(compositionModifiers[style.composition]);
  }

  const fullPrompt = parts.join(", ");
  return generateImage(fullPrompt, outputPath);
}

// Usage
generateWithStyle(
  "A sleek laptop",
  {
    background: { type: "white" },
    lighting: "studio",
    quality: "high",
    composition: "centered",
  },
  "public/images/generated/laptop.png"
);
```

## Preset Templates

```typescript
const presetTemplates: Record<string, StyleConfig> = {
  "product-ecommerce": {
    background: { type: "white" },
    lighting: "studio",
    quality: "high",
    composition: "centered",
  },
  "logo-brand": {
    background: { type: "transparent" },
    quality: "ultra",
    composition: "centered",
  },
  "social-vibrant": {
    background: { type: "auto" },
    lighting: "natural",
    quality: "high",
    composition: "dynamic",
  },
  "artistic-moody": {
    background: { type: "custom", color: "#1a1a2e" },
    lighting: "dramatic",
    quality: "ultra",
    composition: "rule-of-thirds",
  },
};

// Quick access function
async function generateWithPreset(
  prompt: string,
  presetName: keyof typeof presetTemplates,
  outputPath: string
) {
  const preset = presetTemplates[presetName];
  return generateWithStyle(prompt, preset, outputPath);
}

// Usage
generateWithPreset(
  "A premium watch",
  "product-ecommerce",
  "public/images/generated/watch.png"
);
```
