# Benchmark Summary

See also: the [simple benchmark](https://github.com/Moelf/UnROOT_RDataFrame_MiniBenchmark/tree/master/simple_benchmarks) for comparing mostly the read/interpretation speed between ROOT and Julia.
## Results:
### Single-threaded (cold run, first Julia, then RDF):
- [Julia](https://nbviewer.jupyter.org/github/Moelf/UnROOT_RDataFrame_MiniBenchmark/blob/master/UnROOT_benchmark.ipynb): 20.58 s
- [PyROOT in Jupyter](https://nbviewer.jupyter.org/github/Moelf/UnROOT_RDataFrame_MiniBenchmark/blob/master/RDataFrame_benchmark.ipynb) (PyROOT): 40.21 s
- Compiled ROOT loop: 28.16 s
- Compiled ROOT RDataFrame: 19.82 s

See full results and scripts to verify for yourself: [composite_benchmark](https://github.com/Moelf/UnROOT_RDataFrame_MiniBenchmark/tree/master/composite_benchmarks 

(all heavy lifting functions used by RDataFrame are written in C++ inside [header file](https://github.com/Moelf/UnROOT_RDataFrame_MiniBenchmark/blob/master/composite_benchmarks/df103_NanoAODHiggsAnalysis_python.h))

## Specs:
```
Julia Version 1.7.0-beta4
Commit d0c90f37ba (2021-08-24 12:35 UTC)
Platform Info:
  OS: Linux (x86_64-pc-linux-gnu)
  CPU: Intel(R) Xeon(R) W-2265 CPU @ 3.50GHz
```
- 64GB Main Memory @ 3000 MHz

```julia
(composite_benchmarks) pkg> st
      Status
  [68837c9b] FHist v0.6.1
  [7073ff75] IJulia v1.23.2
  [3a55db76] LVCyl v0.1.0 `https://github.com/JuliaHEP/LVCyl.jl#master`
  [f517fe37] Polyester v0.5.1
  [3cd96dde] UnROOT v0.6.2
```
## Physics Task:
Make a histogram of 4-lepton invariant mass (higgs candidate in real analysis)

## Data:
http://opendata.web.cern.ch/record/12341
```julia
julia> mytree
 Row │ nMuon   Muon_pt           Muon_eta          Muon_phi          Muon_mass         Muon_charge     
     │ UInt32  SubArray{Float3   SubArray{Float3   SubArray{Float3   SubArray{Float3   SubArray{Int32, 
─────┼─────────────────────────────────────────────────────────────────────────────────────────────────
 1   │ 2       Float32[10.76369  Float32[1.066827  Float32[-0.03427  Float32[0.105658  Int32[-1, -1]
 2   │ 2       Float32[10.53849  Float32[-0.42778  Float32[-0.27479  Float32[0.105658  Int32[1, -1]
 3   │ 1       Float32[3.275326  Float32[2.210855  Float32[-1.22341  Float32[0.105658  Int32[1]
```

## Procedure
minimized based on: https://root.cern/doc/master/df103__NanoAODHiggsAnalysis_8py.html
1. `nMuon == 4`.
2. All muon has `pt > 5` and `abs(eta) < 2.4`.
3. Sum of `Muon_charge` equals 0.
4. Get the `Z_idx = [[idx1, idx2], [idx3, idx4]]` which represent two pairs of Z-candidate muons.
5. Each Z-candidate muon pair must **not** have `dR < 0.02`.
6. Compute the two Z bosons' masses and require first to be between 40,120 GeV, the second between 12,120 GeV.
7. Compute Higgs' mass and fill Histogram.
