# GSDFiles.jl

A pure-Julia writer/reader for the GSD v2 format (HOOMD schema compatible), tested with OVITO.

## Features
- Write OVITO/HOOMD-compatible `configuration/*` and `particles/*` chunks
- Topology: bonds, angles, dihedrals, impropers
- Row-major compliance for matrices
- Reader for single frames and topology families

## Quick start

```julia
using GSDFiles

w = GSDFiles.GSDWriter("demo.gsd"; application="MyApp", schema="hoomd", schema_version=(1,4))
h = GSDFiles.open_gsd(w)

GSDFiles.write_configuration_step!(h, 0)
GSDFiles.write_configuration_dimensions!(h, 3)
GSDFiles.write_configuration_box!(h, (10f0,10f0,10f0,0,0,0))

N = 2
GSDFiles.write_particles_N!(h, N)
GSDFiles.write_particles_types!(h, ["A","B"])
GSDFiles.write_particles_typeid!(h, UInt32[0,1])
GSDFiles.write_particles_position!(h, Float32[0 0 0; 1 0 0])
GSDFiles.write_particles_velocity!(h, zeros(Float32, 2, 3))
GSDFiles.write_particles_diameter!(h, Float32[1.32, 0.62])
GSDFiles.write_particles_mass!(h, Float32[15.999, 1.008])
GSDFiles.write_particles_charge!(h, Float32[-0.8, 0.4])

GSDFiles.end_frame!(h)
GSDFiles.close_gsd(h)

r = GSDFiles.open_read("demo.gsd")
f = GSDFiles.read_frame(r, 1)
GSDFiles.close(r)
```

The optional `particles/diameter`, `particles/mass`, and `particles/charge`
chunks are exposed as `f.particles.diameter`, `f.particles.mass`, and
`f.particles.charge` when present.
