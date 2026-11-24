# Project Plan: Automated Smartwatch Activity Replication

Ubiquitous Systems Lab, University of Siegen, Winter Semester 2026/27

Team: Abdul Rehman Khan, Zoleko Voufo, Hareem Akram

## 1. What we are building

A smartwatch records how your arm moves (with its accelerometer) when you do an
activity like walking. Our goal is to **play that movement back with a machine**,
so the watch "feels" the same activity again without a person wearing it.

The machine is a **3D printer**. Instead of printing, we use its motors to move
the watch around. A 3D printer is controlled with **G-Code**, a simple text
language of move commands such as `G1 X50 Y20 F3000` ("move to X=50 mm, Y=20 mm
at speed 3000 mm/min").

So the system has three parts:

```
 1. RECORD                    2. CONVERT                     3. REPLAY
 Watch on a person's   ->     Script turns the        ->     3D printer moves the
 wrist saves sensor           sensor data into               watch, which is attached
 data (CSV file)              G-Code (+ optional noise)      to the printer
```

The task description also asks us to **add user-defined noise**. This means the
user can choose how "messy" the replay is (small random changes in speed, size
and timing of the movements), so the replay looks more like a real, imperfect
human and less like a robot.

## 2. Goals

**Must have (minimum for a working project)**
- A watch app that records accelerometer data and lets us download it as CSV.
- A script that converts one recorded activity (walking) into G-Code.
- The printer replays the activity with the watch attached, safely.
- A user setting for the noise level.
- A comparison of the original recording and the replayed recording
  (plots and a few simple numbers).

**Nice to have (if time allows)**
- Replay of 3 or more activities (for example walking, running in place,
  brushing teeth, waving).
- The watch's own step counter counts steps during a replayed walk.
  This is a simple, convincing demo.
- A small menu on the watch to choose the activity and noise level.
- A "plug and go" setup where a small board (for example a Raspberry Pi)
  sends the G-Code to the printer without a laptop.

## 3. Hardware and software

| What | Used for |
|---|---|
| Bangle.js smartwatch (from the lab) | Records accelerometer data; is moved during replay |
| 3D printer (from the lab) | Moves the watch using G-Code |
| Watch holder (3D printed or velcro/strap) | Fixes the watch firmly to the printer bed or print head |
| Espruino Web IDE (espruino.com/ide) | Writing and uploading the watch app (JavaScript) |
| Python 3 (numpy, pandas, matplotlib) | Converting data to G-Code, adding noise, making plots |
| Pronterface or a small Python serial script | Sending G-Code to the printer over USB |
| GitHub (this repo) | Code, data, progress tracking and documentation |

## 4. How we will do it, step by step

### Step 1: Learn the basics (week 1)
- Everyone does the lab safety briefing.
- Everyone finishes the Espruino tutorial and the Tamagotchi starter project, so
  we all know how the Bangle.js works.
- Find out which 3D printer we can use and how to send G-Code to it.
- Read live accelerometer values on the watch (`Bangle.on('accel', ...)`).

### Step 2: Record activities on the watch (week 2)
- Write a small watch app that saves `time, x, y, z` to the watch's storage at a
  fixed rate (start with 25 Hz) while recording is on. A button starts and stops it.
- Download the file and save it as CSV in `data/`.
- Record each activity a few times, about 30 to 60 seconds each.
  Start with **walking**, because it is simple and repeats.

### Step 3: First printer movements (week 3)
- Attach the watch firmly to the printer (bed or print head).
- **Never heat the printer.** We only use the motors.
- Send simple hand-written G-Code: move back and forth on one axis, slowly first.
- Record with the watch during these moves, to see what the watch measures
  and what the printer can and cannot do.

### Step 4: Convert recordings to G-Code (weeks 3 to 4)
We start with the simplest method that works and only improve if needed.

- **Simple method (our main method):** Walking is a repeating movement.
  We split the recording into short windows (for example 2 seconds). For each
  window we find how fast the arm swings (frequency) and how strong (amplitude).
  Then we generate back-and-forth printer moves with the same rhythm.
  A small formula turns acceleration into movement distance:
  `distance = acceleration / (2 * pi * frequency)^2`.
- **Advanced method (only if time allows):** Turn acceleration directly into a
  position path (by integrating twice, with filtering to stop drift) and follow
  that path with many small G-Code moves. This works for any activity but is harder.
- The script also adds **safety limits**: it never moves outside the printer's
  area and never goes faster than the printer allows.

### Step 5: Add user-defined noise (week 5)
The user sets a noise level, for example from 0 (perfect copy) to 1 (very messy).
The script then randomly changes, a little:
- the size of each movement,
- the speed / rhythm (timing),
- the position (small random offsets).

### Step 6: Test and compare (weeks 5 to 6)
- Record with the watch while the printer replays.
- Compare original and replay: plot both signals, compare rhythm (frequency) and
  strength (amplitude), and check if the watch counts steps.
- Write down what works and what does not. Honest results count too.

### Step 7: Polish and present (weeks 7 to 8)
- Clean up the code and add comments.
- Make a simple way to run everything (one command, or a watch menu).
- Record a short demo video as a backup for the live demo.
- Prepare the final slides.

## 5. Timeline

| Dates | Work | Milestone |
|---|---|---|
| 24 Nov to 30 Nov | Step 1: learn basics, safety briefing, printer access | Repo set up, everyone can upload to the watch |
| 1 Dec to 7 Dec | Step 2: recording app, first recordings | First CSV files in `data/` |
| 8 Dec to 14 Dec | Step 3: first printer moves with the watch attached | Printer moves the watch safely |
| 15 Dec to 20 Dec | Step 4: first G-Code from a walking recording | **Midterm demo:** record, convert, replay walking |
| Week of 21 Dec | **Midterm presentation** | |
| 22 Dec to 4 Jan | Holiday break (light work only) | |
| 5 Jan to 11 Jan | Step 5: noise setting; more activities | Noise works |
| 12 Jan to 18 Jan | Step 6: testing and comparison | Plots and results in `results/` |
| 19 Jan to 31 Jan | Step 7: polish, demo video, slides | Final prototype ready |
| Week of 1 Feb | **Final presentation** | |

## 6. Team roles

Everyone writes code, tests in the lab and presents. Each person also owns one area:

| Role | Responsible for | Member |
|---|---|---|
| Watch app | Recording app on the Bangle.js, downloading data | Abdul Rehman Khan |
| Data and G-Code | Python script: CSV to G-Code, noise, safety limits | Hareem Akram |
| Printer and hardware | Watch holder, printer setup, sending G-Code, safety | Zoleko Voufo |
| Testing and documentation | Comparisons, plots, README, slides | Shared by all three |

**How we work together**
- We meet in the lab at least once a week and write short notes in `docs/meetings.md`.
- Tasks are tracked as GitHub Issues. Each person works on a branch and opens a
  pull request, so everyone's work is visible on GitHub.
- We commit small steps often (the GitHub page is graded at midterm and final).

## 7. Repository structure

```
watch/        Espruino (JavaScript) code for the Bangle.js
data/         Recorded activities as CSV files
replicator/   Python scripts: CSV to G-Code, noise, plots
gcode/        Generated G-Code files
results/      Plots and comparison results
docs/         This plan, meeting notes, slides
```

## 8. Risks and what we will do about them

| Risk | What we will do |
|---|---|
| The printer cannot move as fast or as hard as a human arm. | Start with slower activities, scale the movement down if needed, and explain this limit in our results. |
| A printer only moves in straight lines (X, Y, Z). It cannot turn the watch like a wrist does. | Focus on the main movement direction. Clearly state this as a limitation. |
| The Z axis is very slow on most printers. | Use mainly the X and Y axes. |
| The watch comes loose or the printer hits its edge. | Strong holder, safety limits in the script, slow first tests, someone always watching, power switch within reach. |
| Converting acceleration to position drifts. | Use the simple rhythm-based method first. |
| Not enough time. | Get the simple walking case fully working before the midterm. Extra activities are "nice to have". |

## 9. How we will know it works

- The replayed signal has the **same rhythm** as the original (for walking,
  within about 10%).
- The replayed signal has a **similar strength**, or a clearly explained scaled version.
- With noise set to 0 the replay is regular; with higher noise it is visibly more varied.
- The printer stays inside safe limits in every test.
- Stretch goal: the watch counts steps during a replayed walk.

## 10. Questions for our supervisor

1. Which 3D printer can we use, and when?
2. Which Bangle.js version should we use (Bangle.js 1 or 2)?
3. Is it fine to generate the G-Code on a laptop, or should part of the system
   run on a microcontroller?
4. Can we 3D print a holder for the watch in the lab?
