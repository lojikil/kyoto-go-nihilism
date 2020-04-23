# go-ing for an evening stroll
## golang beasts & where to find them

---

# `$ who`

- Stefan Edwards, Practice Lead ComServ
  - GitHub/Twitter/Lobste.rs: lojikil 
- Bobby Tonic, Senior AppSec Engineer
  - Twitter: b0bbytabl3s
  - GitHub: btonic
- Trail of Bits 

<!--

footer: @b0bbytabl3s::@lojikil::@trailofbits
page_number: true
-->

---

# `$ what presentation.md`

- Common Go vulnerabilities we see
  - Integers/Numerics
  - Standard Library Issues
  - Error checking & type assertions
  - Defer semantics
- How to find them
- How to fix them

---

# `$ what takeaway.md`

- Go has some edge cases
- Luckily, most are easily uncovered
- Tooling can help... if you know how to use them

![peak performance](performance-small.png)

---

# Integers/Numerics

- Go's integer semantics are interesting... 

```
        v, err := strconv.Atoi("4294967377")
        g := int32(v)
        fmt.Printf("v: %v, g: %v\n", v, g);
```

- on 32bit: `v` is an `int`, 32bits wide, and truncated before conversion in `g`
- on 64bit: `v` is an `int`, 64bits wide, and only `g` is truncated: `v: 4294967377, g: 81`

<!--

description

-->

---

# Integers/Numerics

- that may seem obvious, but we see it often
  - CNCF projects, blockchains, &c <!-- the code  above was from k8s -->
- Finding it is _mostly_ manual:
  - Any uses of `strconv.Atoi` is likely wrong
  - Flows with `ParseInt/ParseUint` and down casts
  - Nico has done some  great hunts with CodeQL  
<!--
finding
-->

---

# Integers/Numerics

_an aside_

```
if len(s) > 1 && (s[0] == 'E' || s[0] == 'e') {
parsed, err := strconv.ParseInt(string(s[1:]), 10, 64)
	if err != nil {
		return 0, 0, DecimalExponent, false
	}
	return 10, int32(parsed), DecimalExponent, true
}
```

---

# Integers/Numerics

- always check for things that return 'naked' `int`
- ensure what build platform you're using (amd64, &c)
- check dataflows from `strconv.ParseInt` and assume `strconv.Atoi` is wrong
- Tooling? CodeQL, two fists

<!--
fixing
-->

---

# Standard Library

---

# Standard Library

---

# Standard Library

---

# Errors & Assertions

- Go errors are great... 
  - assuming you pay attention
- Type assertions fit in here too
- Four major issues:
```go
v, err := SomeFunc(...)
g, err := SomeFunc(...)
h := someval.(int)
err == nil || err != nil
```

---

# Errors & Assertions

- We see *a lot* of type assertion panics & missed error checks
- Luckily easier to catch
  - use `errcheck -asserts` for both error checks & type assertions
  - use `ineffassign`
  - _maybe_ use `errcheck -assert  -blank`
- Fuzz... **everything** 

---

# Errors & Assertions

- Check your error results rigorously
- Understand your flows as much as  possible
  - Compiler won't save you
  - Can continue on... but  is that ok? 
- Avoid  type assertions, and always check `ok`
  - `if h, ok := someval.(int); ok { ...`
  - always check `ok` is true 

--- 

# Defer Semantics

--- 

# Defer Semantics

--- 

# Defer Semantics

---

# Thanks!

https://github.com/lojikil/kyoto-go-nihilism
