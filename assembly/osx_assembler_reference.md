# Using the Assembler
- a.out format is not standard BSSSD format
- produces Mach-O (Mach object) file format
```
as [option] ... [file] ...
```

## Options
```
-o
--
-f
-g
-v
-n
-l
-L
-V
-W
-dynamic
-static
```

## Architecture Options
```
-arch
-force_cpusubtype_ALL
-arch_multiple
```

## PowerPC-Specific Options
```
-no_ppc601
-static_branch_prediction_Y_bit
-static_branch_prediction_AT_bits
```
