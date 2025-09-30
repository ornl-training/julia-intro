- Trebuchet diagram showing what the quantities used are refering to

02_02
- Add `setindex!` method taking a `Symbol` as index

02_03
- Define `-(::Trebuchet, ::Vector) -> Trebuchet`
- Constrain `aim` to result in meaningful angle:

```julia
function aim(trebuchet, environment; ϵ = 0.1, η = 0.05)
    better_trebuchet = copy(trebuchet)
    hit = x -> (shoot_distance([environment.wind, x[2], x[1]]) - environment.target_distance)
    while abs(hit(better_trebuchet)) > ϵ
        grad = gradient(hit, better_trebuchet)
        better_trebuchet -= η * grad
        better_trebuchet[2] = better_trebuchet[2] % 2pi
        if better_trebuchet[2] < 0
            better_trebuchet[2] += 2pi
        end
    end; return Trebuchet(better_trebuchet...)
end
```

- Could write all-in-one function to use `Blink` with `Trebuchet.visualize` show
  the result after calling with starting parameters

- Show process of adding documentation
    (including with PkgTemplates / Documenter)
