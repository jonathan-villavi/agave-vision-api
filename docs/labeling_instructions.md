📘 AGAVE VISION — LABELING MANUAL

For Label Studio + YOLOv8

Applies to all rounds (tiles_round1, tiles_round2, tiles_round3, tiles_round4)

⸻

0. ⚙️ MANDATORY PRE-WORK — Label Studio Setup (Local Files)

Label Studio does NOT allow local file serving unless you explicitly enable it.
Every annotator must follow these steps before starting a project.

⸻

0.1. Verify dataset folders

All datasets follow the naming convention:

agave-vision-api/data/tiles_round<round_number>/images/

Examples:

.../data/tiles_round1/images/
.../data/tiles_round2/images/
.../data/tiles_round3/images/
.../data/tiles_round4/images/

Each round is independent and requires a separate LS project.

⸻

0.2. Kill any previous Label Studio process (IMPORTANT)

Label Studio sometimes runs as:
• label-studio
• label_studio
• label studio ← this one caused your issue

Run all:

pkill -f label-studio
pkill -f label_studio
pkill -f "label studio"

If nothing is killed, ignore the message.

⸻

0.3. Activate your Python environment

cd /path/to/agave-vision-api
source .agave-vision-venv/bin/activate

⸻

0.4. Clear any conflicting environment variables

unset LABEL_STUDIO_LOCAL_FILES_SERVING_ENABLED
unset LABEL_STUDIO_LOCAL_FILES_DOCUMENT_ROOT
unset LOCAL_FILES_SERVING_ENABLED
unset LOCAL_FILES_DOCUMENT_ROOT

⸻

0.5. Set ALL required environment variables

For round N, the root must be the tiles_roundN directory, NOT the images folder.

Example for Round 1:

export LOCAL_FILES_SERVING_ENABLED=true
export LABEL_STUDIO_LOCAL_FILES_SERVING_ENABLED=true

export LOCAL_FILES_DOCUMENT_ROOT="/Users/<your-username>/path/agave-vision-api/data/tiles_round1"
export LABEL_STUDIO_LOCAL_FILES_DOCUMENT_ROOT="/Users/<your-username>/path/agave-vision-api/data/tiles_round1"

Replace <your-username> and full path accordingly.

⸻

0.6. Start Label Studio

label-studio start

⸻

0.7. In the UI → Connect Local Files

When creating a new dataset connection:
• Absolute File Path must be:

/Users/<your-username>/path/agave-vision-api/data/tiles_round1/images

    •	It MUST be inside the root set in the environment variable.

If root = tiles_round1, then:
• UI path = tiles_round1/images ✓
• UI path = tiles_round1 ✗ (not a directory of images)

⸻

1. 📂 PROJECT NAMING CONVENTION (MANDATORY)

Each Label Studio project name must follow:

agave-vision-tiles-round-<round_number>

Examples:

agave-vision-tiles-round-1
agave-vision-tiles-round-2
agave-vision-tiles-round-3
agave-vision-tiles-round-4

Use exactly this format for team consistency.

⸻

2. 📦 DATASET NAMING CONVENTION

Inside Label Studio → Cloud Storage → Local Files:

Use storage titles of the form:

tiles-round-<round_number>

Examples:

tiles-round-1
tiles-round-2
tiles-round-3
tiles-round-4

Again: same format for all annotators.

⸻

3. 🖼️ REQUIRED LABELING INTERFACE (XML)

Every project MUST use this annotation interface exactly as written:

<View>
  <Image name="image" value="$image"/>

  <RectangleLabels name="label" toName="image">
    <Label value="pina" background="green"/>
    <Label value="worker" background="yellow"/>
    <Label value="object" background="red"/>
  </RectangleLabels>
</View>

Notes:
• Label names must match model training target classes exactly:
• pina
• worker
• object
• Background colors are important for annotation clarity.

⸻

4. 🎯 ANNOTATION OBJECTIVE

We are training a YOLOv8 model to detect:
• pina
• worker
• object

We also need LS to export both:
• bounding boxes
• no-label “empty” tiles

Empty tiles must remain empty (no boxes).

⸻

5. 🏷️ CLASSES (DEFINITIONS)

pina

Recognizable agave piña (whole or partial, close or far).

worker

Person wearing PPE (helmet, vest, boots, worker posture).

object

Any foreign, removable non-structural item:
• Cones
• Tools
• Loose hardhats
• Hoses/cables on ground
• Tires not attached to truck
• Boards, crates, rocks
• Debris chunks

⸻

6. 🛑 NEVER LABEL THESE
   • Hopper/diffuser walls
   • Floor mesh patterns
   • Concrete walls
   • Railings / metal frames
   • Truck body or fixed tires
   • Permanent cables
   • Shadows
   • Reflections
   • Water stains
   • Tiny debris
   • Ambiguous shapes

⸻

7. ✔️ LABELING RULES

worker

Label if:
• Worker identifiable by PPE
• Upper body visible
• Legs + vest visible
• Worker bending over
• Partially cropped but identifiable

Do NOT label:
• Unidentifiable limbs
• Shadows
• Reflections
• Tiny fragments

⸻

pina

Label:
• Full piñas
• Partial piñas
• Far-away piñas if shape is clear
• Overlapping piñas (each visible one)

Do NOT label:
• Chips
• Indistinguishable mass piles
• Wood chunks that look similar

⸻

object

Label:
• Hardhat on ground
• Cones
• Tools
• Cables lying across floor
• Loose tires
• Crates, boards, buckets
• Large debris

Do NOT label:
• Attached truck components
• Structural pipes
• Permanent cables
• Dirt/small debris

⸻

8. 🔳 EMPTY TILE HANDLING

If tile has none of:
• pina
• worker
• object

→ leave annotation list empty.

Empty tiles are essential for training.

⸻

9. 🧩 DECISION LOGIC

Is it a worker?
→ Yes → worker
→ No →
Is it a pina?
→ Yes → pina
→ No →
Is it a removable object?
→ Yes → object
→ No →
Leave empty

⸻

10. ✔️ Quick Example Mapping

Scenario Result Class
Worker seated, helmet visible label worker
Worker leg + vest label worker
Piñas clear view label pina
Piñas far away but identifiable label pina
Hardhat on ground label object
Cone on walkway label object
Cable across floor label object
Truck bed, no objects empty —
Black pit empty —
Metal plate empty —
Shadows or glare ignore —
