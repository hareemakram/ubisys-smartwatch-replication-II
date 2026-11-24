# ubisys-smartwatch-replication-II
Collaborative GitHub repository for the Smartwatch Replication II project in the UbiSys Lab course at the University of Siegen (Winter Semester 2026/27).

## About the project
A smartwatch (Bangle.js) records arm movements during an activity such as walking.
We turn that recording into G-Code so a 3D printer can move the watch the same way
and "replay" the activity. The user can add noise so the replay looks more like a real person.

**Read our plan:** [docs/PROJECT_PLAN.md](docs/PROJECT_PLAN.md)

## Repository structure
| Folder | Contents |
|---|---|
| `watch/` | Espruino (JavaScript) code for the Bangle.js |
| `data/` | Recorded activities (CSV) |
| `replicator/` | Python scripts: CSV to G-Code, noise, plots |
| `gcode/` | Generated G-Code files |
| `results/` | Plots and comparison results |
| `docs/` | Plan, meeting notes, slides |

## Team
| Member | Main role |
|---|---|
| Abdul Rehman Khan | Watch app (Bangle.js recording) |
| Hareem Akram | Data processing and G-Code |
| Zoleko Voufo | Printer and hardware |

Testing and documentation are shared by all three members.
