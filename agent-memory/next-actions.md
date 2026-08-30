# Next Actions

Execute these actions in order:

1. Complete and accept `DESIGN.md` plus the Part 0 Client shell OpenDesign
   handoff.
2. Write and accept the bite-sized exact frontend-first Part 0 implementation
   plan, including RED/GREEN commands, contract generation, repository-local
   commits, and the final live sibling smoke gate.
3. Execute the accepted plan from the frontend foundation; do not begin backend
   runtime implementation early.
4. Keep frontend API simulation at the network/test/dev-preview boundary and run
   the production-import guard before each frontend commit.
5. After the usable frontend prototype passes its gates, implement the FastAPI and
   PostgreSQL health slice through the plan's backend RED/GREEN steps.
6. Run the single sibling-repository smoke command and update all agent-memory
   evidence labels with results actually observed.
7. Choose and configure the `kcms-planning` remote separately without publishing
   secrets.
