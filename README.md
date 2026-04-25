# Prompt


You are an expert React + TypeScript developer completing a drag-and-drop UI builder.

**EXISTING CODE (DO NOT CHANGE)**:
✅ Already implemented:
- src/types/index.ts — All TypeScript interfaces (ComponentDefinition, DroppedComponent, CanvasState)
- src/utils/componentRegistry.ts — 3+ components defined (button, input, textarea) with properties and categories
- src/components/ComponentLibrary.tsx — Fully functional sidebar with search, categories, draggable items
- src/hooks/useDragDrop.ts — All hook functions: handleDragOver, handleDropNewComponent, handleDragStartLibrary, etc.
- src/store/canvasStore.ts — Partially implemented (has basic structure)

**WHAT YOU NEED TO BUILD** (Phase 1 Complete MVP):

**1. COMPLETE THE ZUSTAND STORE** — src/store/canvasStore.ts
   Must include ALL these actions:
   - addComponent(componentId: string, x: number, y: number): Add component at position, auto-generate unique id
   - removeComponent(id: string): Delete component from canvas
   - selectComponent(id: string | null): Set selected component (for highlighting)
   - updateComponentProps(id: string, props: Record<string, any>): Update props of selected component
   - updateComponentPosition(id: string, x: number, y: number): Update x,y position when dragging on canvas
   - updateComponentSize(id: string, width: string | number, height: string | number): Update width/height
   - setDragging(isDragging: boolean): Toggle dragging state
   - clearCanvas(): Remove all components
   - getComponentById(id: string): Return component by id

   State structure:
   {
     components: DroppedComponent[],
     selectedComponentId: string | null,
     isDragging: boolean
   }

**2. BUILD THE CANVAS** — src/components/Canvas.tsx
   Requirements:
   - Show 1200x800px canvas area (gray background) with border
   - Render all components from store with absolute positioning (position: absolute, left: x, top: y)
   - When component dropped from sidebar → appears at drop position
   - Click on canvas component → selectComponent(id), show 2px blue outline
   - Drag canvas component → updateComponentPosition(id, newX, newY)
   - Show grid of component bounds with light gray background
   - Handle onDragOver and onDrop using useDragDrop hook
   - Handle onMouseDown to start dragging components on canvas
   - Delete key (Delete/Backspace) → removeComponent(selectedComponentId)

**3. BUILD PROPERTY PANEL** — src/components/PropertyPanel.tsx
   Requirements:
   - Show "No component selected" if selectedComponentId is null
   - When component selected, show all its properties from componentRegistry[componentId].properties
   - For each property, render appropriate editor:
     * type: 'text' → <input type="text" />
     * type: 'number' → <input type="number" />
     * type: 'color' → <input type="color" />
     * type: 'select' → <select> with options
     * type: 'boolean' → <input type="checkbox" />
   - On change → call updateComponentProps(selectedComponentId, { [propertyName]: newValue })
   - Also show editable size properties:
     * Width input (e.g., "120px")
     * Height input (e.g., "40px")
     * X position input
     * Y position input
   - All changes apply in real-time to canvas

**4. BUILD CODE PREVIEW/EXPORT** — src/components/CodePreview.tsx
   Requirements:
   - Modal or panel that shows generated React code
   - Export function: Convert all components in store to React JSX
   - Generated code format (example):
     export function DesignedUI() {
       return (
         <div className="relative w-full h-full bg-gray-50">
           <button className="absolute left-[100px] top-[200px] px-4 py-2 bg-blue-500 text-white rounded-lg">
             Click me
           </button>
           <input 
             type="text" 
             placeholder="Enter text" 
             className="absolute left-[250px] top-[250px] px-3 py-2 border border-gray-300 rounded"
           />
         </div>
       );
     }
   - Button: "Copy Code" → copies to clipboard (show toast "Copied!")
   - Button: "Download Code" → downloads as React component file
   - Show number of components and canvas dimensions

**5. BUILD CODE GENERATOR UTIL** — src/utils/codeGenerator.ts
   Requirements:
   - Function generateCode(components: DroppedComponent[], registry: Record<string, ComponentDefinition>): string
   - For each component, generate:
     * Correct HTML tag (button, input, textarea, etc. based on componentId)
     * Absolute positioning: left-[{x}px] top-[{y}px] (using Tailwind arbitrary values)
     * Width/height: w-[{width}px] h-[{height}px]
     * Props rendered as HTML attributes (e.g., placeholder, type, disabled, etc.)
     * Apply component variant styling (e.g., button variant="primary" → bg-blue-500 text-white)
   - Output must be valid, copy-paste-able React JSX
   - Include export statement and function wrapper

**6. RENDER ACTUAL COMPONENTS ON CANVAS** — Update Canvas.tsx
   When rendering dropped components, show actual component UI:
   - Button: <button>{props.text}</button> with variant styling
   - Input: <input placeholder={props.placeholder} type={props.type} />
   - Textarea: <textarea placeholder={props.placeholder} rows={props.rows} />
   - Apply Tailwind classes based on variant and props
   - Ensure components are interactive (can click button, type in input, etc. during design)

**7. UPDATE MAIN TOOLBAR** — src/components/MainToolbar.tsx
   Add buttons:
   - "Export Code" → open CodePreview modal
   - "Clear Canvas" → clearCanvas()
   - Show selected component name (if any) in toolbar

**CONSTRAINTS & REQUIREMENTS**:
- All TypeScript, no any types (use proper interfaces from types/index.ts)
- Absolute positioning only (left/top with px)
- Tailwind CSS for all styling (no inline CSS objects except position values)
- No external UI component libraries (no shadcn, antd, etc.)
- Smooth drag interactions (no visual jank)
- Real-time updates: change property → canvas updates instantly
- Delete key works globally (add KeyboardEvent listener in Canvas)
- Must integrate with existing useDragDrop hook (don't rewrite it)
- ComponentLibrary must remain unchanged
- TypeScript strict mode: no implicit any, proper types everywhere

**DELIVERABLES**:
1. src/store/canvasStore.ts — Complete store with all actions
2. src/components/Canvas.tsx — Canvas rendering + drag + selection logic
3. src/components/PropertyPanel.tsx — Property editor for selected component
4. src/components/CodePreview.tsx — Code export modal
5. src/utils/codeGenerator.ts — JSX generation logic
6. src/components/MainToolbar.tsx — Toolbar buttons for export/clear

**TEST REQUIREMENTS** (Verify these work):
✅ Drag Button from sidebar → appears on canvas at cursor position
✅ Click Button on canvas → shows blue outline, Property Panel shows props
✅ Edit "Button Text" in PropertyPanel → button text updates on canvas in real-time
✅ Edit width/height → button size changes
✅ Drag button on canvas → position updates in real-time
✅ Delete button (press Delete key) → button removed from canvas
✅ Add multiple components (button + input) → both visible on canvas
✅ Click Export Code → valid React JSX shown, copy to clipboard works
✅ No console errors
✅ No TypeScript errors

**INTEGRATION NOTES**:
- Store must work with existing useDragDrop hook methods
- ComponentLibrary component should NOT be changed
- Keep component registry structure exactly as-is
- Use store hooks in components: const { components, selectedComponentId, ...actions } = useCanvasStore()
- Make Canvas droppable using handleDragOver and handleDropNewComponent from hook

Start implementation. For each file, show the complete code with explanations of key logic.
