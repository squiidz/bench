Go HTTP Router Benchmark
========================

This benchmark suite aims to compare the performance of HTTP request routers for [Go](https://golang.org) by implementing the routing structure of some real world APIs.
Some of the APIs are slightly adapted, since they can not be implemented 1:1 in some of the routers.

Of course the tested routers can be used for any kind of HTTP request → handler function routing, not only (REST) APIs.


#### Tested routers & frameworks:

 * [Ace](https://github.com/plimble/ace)
 * [Beego](http://beego.me/)
 * [Bone](https://github.com/squiidz/bone)
 * [Denco](https://github.com/naoina/denco)
 * [Gin](http://gin-gonic.github.io/gin/)
 * [Gocraft Web](https://github.com/gocraft/web)
 * [Goji](https://github.com/zenazn/goji/)
 * [Go-Json-Rest](https://github.com/ant0ine/go-json-rest)
 * [go-restful](https://github.com/emicklei/go-restful)
 * [Gorilla Mux](http://www.gorillatoolkit.org/pkg/mux)
 * [http.ServeMux](http://golang.org/pkg/net/http/#ServeMux)
 * [HttpRouter](https://github.com/julienschmidt/httprouter)
 * [HttpTreeMux](https://github.com/dimfeld/httptreemux)
 * [Kocha-urlrouter](https://github.com/naoina/kocha-urlrouter)
 * [Macaron](http://macaron.gogs.io/docs/intro/)
 * [Martini](https://github.com/go-martini/martini)
 * [Pat](https://github.com/bmizerany/pat)
 * [Revel](https://github.com/revel/revel)
 * [Rivet](https://github.com/typepress/rivet)
 * [TigerTonic](https://github.com/rcrowley/go-tigertonic)
 * [Traffic](https://github.com/pilu/traffic)
 * [Vulcan](https://github.com/mailgun/route)
 * [Zeus](https://github.com/daryl/zeus)


## Motivation

Go is a great language for web applications. Since the [default *request multiplexer*](http://golang.org/pkg/net/http/#ServeMux) of Go's net/http package is very simple and limited, an accordingly high number of HTTP request routers exist.

Unfortunately, most of the (early) routers use pretty bad routing algorithms. Moreover, many of them are very wasteful with memory allocations, which can become a problem in a language with Garbage Collection like Go, since every (heap) allocation results in more work for the Garbage Collector.

Lately more and more bloated frameworks pop up, outdoing one another in the number of features. This benchmark tries to measure their overhead.

Beware that we are comparing apples to oranges here, we compare feature-rich frameworks to packages with simple routing functionality only. But since we are only interested in decent request routing, I think this is not entirely unfair. The frameworks are configured to do as little additional work as possible.

If you care about performance, this benchmark can maybe help you find the right router, which scales with your application.

Personally, I prefer slim and optimized software, which is why I implemented [HttpRouter](https://github.com/julienschmidt/httprouter), which is also tested here. In fact, this benchmark suite started as part of the packages tests, but was then extended to a generic benchmark suite.
So keep in mind, that I am not completely unbiased :relieved:


## Results

Benchmark System:
 * Intel Core i5 M 580 (4x 2.67GHz)
 * 2x 4 GiB DDR3-1066 RAM
 * go1.4 linux/amd64
 * Arch Linux amd64 (Linux Kernel 3.17.6)


### Memory Consumption

Besides the micro-benchmarks, there are 3 sets of benchmarks where we play around with clones of some real-world APIs, and one benchmark with static routes only, to allow a comparison with [http.ServeMux](http://golang.org/pkg/net/http/#ServeMux).
The following table shows the memory required only for loading the routing structure for the respective API.
The best 3 values for each test are bold. I'm pretty sure you can detect a pattern :wink:

| Router       | Static    | GitHub     | Google+   | Parse     |
|:-------------|----------:|-----------:|----------:|----------:|
| HttpServeMux |  17728 B  |         -  |        -  |        -  |
| Ace          |  37720 B  |   60520 B  |   4280 B  |   7968 B  |
| Beego        | 103240 B  |  165192 B  |  11368 B  |  21040 B  |
| Bone         |  30160 B  |   44576 B  | __3040 B__|   5648 B  |
| Denco        | __9696 B__| __36728 B__|   3256 B  | __4184 B__|
| Gin          |  41760 B  |   66896 B  |   4800 B  |   8712 B  |
| Gocraft Web  |  51304 B  |   88856 B  |   6840 B  |  11936 B  |
| Goji         |  27536 B  |   47256 B  |   3216 B  |   5536 B  |
| Go-Json-Rest | 130944 B  |  128032 B  |  11104 B  |  13760 B  |
| go-restful   | 886200 B  | 1369208 B  |  89176 B  | 142856 B  |
| Gorilla Mux  | 668512 B  | 1493072 B  |  71056 B  | 122136 B  |
| HttpRouter   |  25064 B  |   44184 B  |   3144 B  |   5792 B  |
| HttpTreeMux  |  73288 B  |   74992 B  |   7120 B  |   7616 B  |
| Kocha        | 114960 B  |  785264 B  | 128880 B  | 181712 B  |
| Macaron      |  51752 B  |  103624 B  |   8264 B  |  13384 B  |
| Martini      | 309040 B  |  476992 B  |  23904 B  |  45952 B  |
| Pat          |__15704 B__| __15832 B__| __1448 B__| __1976 B__|
| Revel        |  93808 B  |  141504 B  |  10768 B  |  15488 B  |
| Rivet        |  42872 B  |  110080 B  |   7896 B  |  13496 B  |
| TigerTonic   |  80112 B  |   96240 B  |   9408 B  |   9840 B  |
| Traffic      | 624416 B  | 1053536 B  |  49504 B  |  93496 B  |
| Vulcan       | 624416 B  |  425792 B  |  25496 B  |  44296 B  |
| Zeus         |__14560 B__| __15840 B__| __1456 B__| __1984 B__|

The first place goes to [Pat](https://github.com/bmizerany/pat), followed by [HttpRouter](https://github.com/julienschmidt/httprouter) and [Goji](https://github.com/zenazn/goji/). Now, before everyone starts reading the documentation of Pat, `[SPOILER]` this low memory consumption comes at the price of relatively bad routing performance. The routing structure of Pat is simple - probably too simple. `[/SPOILER]`.

Moreover main memory is cheap and usually not a scarce resource. As long as the router doesn't require Megabytes of memory, it should be no deal breaker. But it gives us a first hint how efficient or wasteful a router works.


### Static Routes

The `Static` benchmark is not really a clone of a real-world API. It is just a collection of random static paths inspired by the structure of the Go directory. It might not be a realistic URL-structure.

The only intention of this benchmark is to allow a comparison with the default router of Go's net/http package, [http.ServeMux](http://golang.org/pkg/net/http/#ServeMux), which is limited to static routes and does not support parameters in the route pattern.

In the `StaticAll` benchmark each of 157 URLs is called once per repetition (op, *operation*). If you are unfamiliar with the `go test -bench` tool, the first number is the number of repetitions the `go test` tool made, to get a test running long enough for measurements. The second column shows the time in nanoseconds that a single repetition takes. The third number is the amount of heap memory allocated in bytes, the last one the average number of allocations made per repetition.

The logs below show, that http.ServeMux has only medium performance, compared to more feature-rich routers. The fastest router only needs 1.8% of the time http.ServeMux needs.

[HttpRouter](https://github.com/julienschmidt/httprouter) was the first router (I know of) that managed to serve all the static URLs without a single heap allocation. Since [the first run of this benchmark](https://github.com/julienschmidt/go-http-routing-benchmark/blob/0eb78904be13aee7a1e9f8943386f7c26b9d9d79/README.md) more routers followed this trend and were optimized in the same way.

```
BenchmarkHttpServeMux_StaticAll     2000           1083520 ns/op              96 B/op          8 allocs/op

BenchmarkAce_StaticAll             30000             41254 ns/op               0 B/op          0 allocs/op
BenchmarkBeego_StaticAll           10000            316800 ns/op           57744 B/op       1099 allocs/op
BenchmarkBone_StaticAll            20000             61164 ns/op            4736 B/op         48 allocs/op
BenchmarkDenco_StaticAll          100000             16256 ns/op               0 B/op          0 allocs/op
BenchmarkGin_StaticAll             30000             40553 ns/op               0 B/op          0 allocs/op
BenchmarkGocraftWeb_StaticAll      10000            216057 ns/op           47696 B/op        942 allocs/op
BenchmarkGoji_StaticAll            10000            107313 ns/op               0 B/op          0 allocs/op
BenchmarkGoJsonRest_StaticAll       1000           1357206 ns/op          169567 B/op       4553 allocs/op
BenchmarkGoRestful_StaticAll         200           6560934 ns/op          294346 B/op       5007 allocs/op
BenchmarkGorillaMux_StaticAll       1000           2341979 ns/op           72944 B/op       1264 allocs/op
BenchmarkHttpRouter_StaticAll      50000             25481 ns/op               0 B/op          0 allocs/op
BenchmarkHttpTreeMux_StaticAll     50000             25453 ns/op               0 B/op          0 allocs/op
BenchmarkKocha_StaticAll           50000             27373 ns/op               0 B/op          0 allocs/op
BenchmarkMacaron_StaticAll          5000            491792 ns/op          118032 B/op       1256 allocs/op
BenchmarkMartini_StaticAll           300           4421434 ns/op          140352 B/op       2335 allocs/op
BenchmarkPat_StaticAll              1000           2010572 ns/op          533904 B/op      11123 allocs/op
BenchmarkRevel_StaticAll            2000           1127954 ns/op          204528 B/op       3925 allocs/op
BenchmarkRivet_StaticAll           10000            100330 ns/op           17584 B/op        314 allocs/op
BenchmarkTigerTonic_StaticAll      20000             76555 ns/op            7504 B/op        157 allocs/op
BenchmarkTraffic_StaticAll           100          10982528 ns/op         3713994 B/op      27161 allocs/op
BenchmarkVulcan_StaticAll            100          11028290 ns/op         3713993 B/op      27161 allocs/op
BenchmarkZeus_StaticAll             3000            550942 ns/op            4736 B/op         48 allocs/op
```

### Micro Benchmarks

The following benchmarks measure the cost of some very basic operations.

In the first benchmark, only a single route, containing a parameter, is loaded into the routers. Then a request for a URL matching this pattern is made and the router has to call the respective registered handler function. End.
```
BenchmarkAce_Param               5000000               267 ns/op              32 B/op          1 allocs/op
BenchmarkBeego_Param             1000000              2856 ns/op             720 B/op         10 allocs/op
BenchmarkBone_Param              1000000              2231 ns/op             656 B/op         10 allocs/op
BenchmarkDenco_Param             5000000               251 ns/op              32 B/op          1 allocs/op
BenchmarkGin_Param               5000000               277 ns/op              32 B/op          1 allocs/op
BenchmarkGocraftWeb_Param        1000000              2266 ns/op             656 B/op          9 allocs/op
BenchmarkGoji_Param              1000000              1179 ns/op             336 B/op          2 allocs/op
BenchmarkGoJsonRest_Param         200000              9802 ns/op            1712 B/op         33 allocs/op
BenchmarkGoRestful_Param          500000              3841 ns/op             568 B/op         16 allocs/op
BenchmarkGorillaMux_Param         500000              4043 ns/op             784 B/op          9 allocs/op
BenchmarkHttpRouter_Param       10000000               181 ns/op              32 B/op          1 allocs/op
BenchmarkHttpTreeMux_Param       2000000               878 ns/op             336 B/op          2 allocs/op
BenchmarkKocha_Param             3000000               433 ns/op              56 B/op          3 allocs/op
BenchmarkMacaron_Param            500000              4479 ns/op            1144 B/op         13 allocs/op
BenchmarkMartini_Param            200000              8350 ns/op            1152 B/op         12 allocs/op
BenchmarkPat_Param               1000000              2416 ns/op             656 B/op         14 allocs/op
BenchmarkRevel_Param              200000              8216 ns/op            1672 B/op         28 allocs/op
BenchmarkRivet_Param             1000000              1377 ns/op             464 B/op          5 allocs/op
BenchmarkTigerTonic_Param         500000              4159 ns/op             992 B/op         19 allocs/op
BenchmarkTraffic_Param            300000              6792 ns/op            1984 B/op         23 allocs/op
BenchmarkVulcan_Param            1000000              1031 ns/op              98 B/op          3 allocs/op
BenchmarkZeus_Param              1000000              2699 ns/op             696 B/op         13 allocs/op
```

Same as before, but now with multiple parameters, all in the same single route. The intention is to see how the routers scale with the number of parameters. The values of the parameters must be passed to the handler function somehow, which requires allocations. Let's see how clever the routers solve this task with a route containing 5 and 20 parameters:
```
BenchmarkAce_Param5              2000000               635 ns/op             160 B/op          1 allocs/op
BenchmarkBeego_Param5             500000              4092 ns/op             992 B/op         13 allocs/op
BenchmarkBone_Param5             1000000              1672 ns/op             528 B/op          5 allocs/op
BenchmarkDenco_Param5            2000000               684 ns/op             160 B/op          1 allocs/op
BenchmarkGin_Param5              2000000               644 ns/op             160 B/op          1 allocs/op
BenchmarkGocraftWeb_Param5       1000000              3518 ns/op             928 B/op         12 allocs/op
BenchmarkGoji_Param5             1000000              1835 ns/op             336 B/op          2 allocs/op
BenchmarkGoJsonRest_Param5        100000             16112 ns/op            3168 B/op         44 allocs/op
BenchmarkGoRestful_Param5         500000              4437 ns/op             568 B/op         16 allocs/op
BenchmarkGorillaMux_Param5        300000              6467 ns/op             912 B/op          9 allocs/op
BenchmarkHttpRouter_Param5       3000000               555 ns/op             160 B/op          1 allocs/op
BenchmarkHttpTreeMux_Param5      1000000              1327 ns/op             336 B/op          2 allocs/op
BenchmarkKocha_Param5            1000000              1871 ns/op             440 B/op         10 allocs/op
BenchmarkMacaron_Param5           300000              5692 ns/op            1416 B/op         16 allocs/op
BenchmarkMartini_Param5           100000             18164 ns/op            1280 B/op         12 allocs/op
BenchmarkPat_Param5               300000              4787 ns/op            1408 B/op         25 allocs/op
BenchmarkRevel_Param5             200000              9986 ns/op            2024 B/op         35 allocs/op
BenchmarkRivet_Param5            1000000              2221 ns/op             528 B/op          9 allocs/op
BenchmarkTigerTonic_Param5        200000             13293 ns/op            2519 B/op         53 allocs/op
BenchmarkTraffic_Param5           200000             10399 ns/op            2280 B/op         31 allocs/op
BenchmarkVulcan_Param5           1000000              1387 ns/op              98 B/op          3 allocs/op
BenchmarkZeus_Param5             1000000              1822 ns/op             544 B/op          6 allocs/op

BenchmarkAce_Param20             1000000              1876 ns/op             640 B/op          1 allocs/op
BenchmarkBeego_Param20            100000             12704 ns/op            3867 B/op         17 allocs/op
BenchmarkBone_Param20            1000000              2261 ns/op             784 B/op          5 allocs/op
BenchmarkDenco_Param20           1000000              2285 ns/op             640 B/op          1 allocs/op
BenchmarkGin_Param20             1000000              1888 ns/op             640 B/op          1 allocs/op
BenchmarkGocraftWeb_Param20       200000             11342 ns/op            3803 B/op         16 allocs/op
BenchmarkGoji_Param20             300000              5755 ns/op            1247 B/op          2 allocs/op
BenchmarkGoJsonRest_Param20        30000             40293 ns/op           10461 B/op         78 allocs/op
BenchmarkGoRestful_Param20        300000              5314 ns/op             568 B/op         16 allocs/op
BenchmarkGorillaMux_Param20       100000             14199 ns/op            3277 B/op         11 allocs/op
BenchmarkHttpRouter_Param20      1000000              1790 ns/op             640 B/op          1 allocs/op
BenchmarkHttpTreeMux_Param20      200000              7524 ns/op            2188 B/op          4 allocs/op
BenchmarkKocha_Param20            300000              6193 ns/op            1808 B/op         27 allocs/op
BenchmarkMacaron_Param20          100000             14366 ns/op            4291 B/op         20 allocs/op
BenchmarkMartini_Param20           20000             68579 ns/op            3645 B/op         14 allocs/op
BenchmarkPat_Param20              300000              4796 ns/op            1408 B/op         25 allocs/op
BenchmarkRevel_Param20            100000             20410 ns/op            5552 B/op         54 allocs/op
BenchmarkRivet_Param20            200000              9790 ns/op            2619 B/op         26 allocs/op
BenchmarkTigerTonic_Param20        30000             52599 ns/op           10541 B/op        178 allocs/op
BenchmarkTraffic_Param20           50000             32802 ns/op            8000 B/op         66 allocs/op
BenchmarkVulcan_Param20          1000000              2303 ns/op              98 B/op          3 allocs/op
BenchmarkZeus_Param20            1000000              2461 ns/op             784 B/op          6 allocs/op
```

Now let's see how expensive it is to access a parameter. The handler function reads the value (by the name of the parameter, e.g. with a map lookup; depends on the router) and writes it to our [web scale storage](https://www.youtube.com/watch?v=b2F-DItXtZs) (`/dev/null`).
```
BenchmarkAce_ParamWrite          5000000               404 ns/op              40 B/op          2 allocs/op
BenchmarkBeego_ParamWrite        1000000              2952 ns/op             728 B/op         11 allocs/op
BenchmarkBone_ParamWrite          500000              3796 ns/op            1072 B/op         13 allocs/op
BenchmarkDenco_ParamWrite        5000000               328 ns/op              32 B/op          1 allocs/op
BenchmarkGin_ParamWrite          3000000               418 ns/op              40 B/op          2 allocs/op
BenchmarkGocraftWeb_ParamWrite   1000000              2470 ns/op             664 B/op         10 allocs/op
BenchmarkGoji_ParamWrite         1000000              1270 ns/op             336 B/op          2 allocs/op
BenchmarkGoJsonRest_ParamWrite    200000             11968 ns/op            2184 B/op         38 allocs/op
BenchmarkGoRestful_ParamWrite     500000              3834 ns/op             568 B/op         16 allocs/op
BenchmarkGorillaMux_ParamWrite    500000              4391 ns/op             792 B/op         10 allocs/op
BenchmarkHttpRouter_ParamWrite   5000000               275 ns/op              32 B/op          1 allocs/op
BenchmarkHttpTreeMux_ParamWrite  2000000               951 ns/op             336 B/op          2 allocs/op
BenchmarkKocha_ParamWrite        3000000               518 ns/op              56 B/op          3 allocs/op
BenchmarkMacaron_ParamWrite       500000              4968 ns/op            1208 B/op         15 allocs/op
BenchmarkMartini_ParamWrite       200000              9632 ns/op            1256 B/op         16 allocs/op
BenchmarkPat_ParamWrite           500000              4167 ns/op            1088 B/op         19 allocs/op
BenchmarkRevel_ParamWrite         200000              9273 ns/op            2128 B/op         33 allocs/op
BenchmarkRivet_ParamWrite        1000000              1649 ns/op             472 B/op          6 allocs/op
BenchmarkTigerTonic_ParamWrite    200000              6512 ns/op            1440 B/op         25 allocs/op
BenchmarkTraffic_ParamWrite       200000              8410 ns/op            2400 B/op         27 allocs/op
BenchmarkVulcan_ParamWrite       1000000              1030 ns/op              98 B/op          3 allocs/op
BenchmarkZeus_ParamWrite          500000              4210 ns/op            1112 B/op         16 allocs/op
```

### [Parse.com](https://parse.com/docs/rest#summary)

Enough of the micro benchmark stuff. Let's play a bit with real APIs. In the first set of benchmarks, we use a clone of the structure of [Parse](https://parse.com)'s decent medium-sized REST API, consisting of 26 routes.

The tasks are 1.) routing a static URL (no parameters), 2.) routing a URL containing 1 parameter, 3.) same with 2 parameters, 4.) route all of the routes once (like the StaticAll benchmark, but the routes now contain parameters).

Worth noting is, that the requested route might be a good case for some routing algorithms, while it is a bad case for another algorithm. The values might vary slightly depending on the selected route.

```
BenchmarkAce_ParseStatic                10000000               127 ns/op               0 B/op          0 allocs/op
BenchmarkBeego_ParseStatic               1000000              1800 ns/op             368 B/op          7 allocs/op
BenchmarkBone_ParseStatic                2000000               695 ns/op             144 B/op          3 allocs/op
BenchmarkDenco_ParseStatic              20000000              58.0 ns/op               0 B/op          0 allocs/op
BenchmarkGin_ParseStatic                10000000               143 ns/op               0 B/op          0 allocs/op
BenchmarkGocraftWeb_ParseStatic          1000000              1265 ns/op             304 B/op          6 allocs/op
BenchmarkGoji_ParseStatic                3000000               438 ns/op               0 B/op          0 allocs/op
BenchmarkGoJsonRest_ParseStatic           200000              8209 ns/op            1064 B/op         29 allocs/op
BenchmarkGoRestful_ParseStatic            100000             14952 ns/op            2904 B/op         32 allocs/op
BenchmarkGorillaMux_ParseStatic           500000              3905 ns/op             464 B/op          8 allocs/op
BenchmarkHttpRouter_ParseStatic         30000000              55.3 ns/op               0 B/op          0 allocs/op
BenchmarkHttpTreeMux_ParseStatic        20000000              87.9 ns/op               0 B/op          0 allocs/op
BenchmarkKocha_ParseStatic              20000000              81.7 ns/op               0 B/op          0 allocs/op
BenchmarkMacaron_ParseStatic             1000000              2974 ns/op             752 B/op          8 allocs/op
BenchmarkMartini_ParseStatic              300000              6952 ns/op             832 B/op         11 allocs/op
BenchmarkPat_ParseStatic                 1000000              1027 ns/op             240 B/op          5 allocs/op
BenchmarkRevel_ParseStatic                300000              6923 ns/op            1288 B/op         25 allocs/op
BenchmarkRivet_ParseStatic               3000000               537 ns/op             112 B/op          2 allocs/op
BenchmarkTigerTonic_ParseStatic          5000000               356 ns/op              48 B/op          1 allocs/op
BenchmarkTraffic_ParseStatic              200000              7521 ns/op            2344 B/op         25 allocs/op
BenchmarkVulcan_ParseStatic               200000              7496 ns/op            2344 B/op         25 allocs/op
BenchmarkZeus_ParseStatic                1000000              1521 ns/op             256 B/op          6 allocs/op

BenchmarkAce_ParseParam                  5000000               334 ns/op              64 B/op          1 allocs/op
BenchmarkBeego_ParseParam                1000000              2886 ns/op             736 B/op         10 allocs/op
BenchmarkBone_ParseParam                 1000000              2659 ns/op             752 B/op         11 allocs/op
BenchmarkDenco_ParseParam                5000000               347 ns/op              64 B/op          1 allocs/op
BenchmarkGin_ParseParam                  5000000               344 ns/op              64 B/op          1 allocs/op
BenchmarkGocraftWeb_ParseParam           1000000              2376 ns/op             672 B/op          9 allocs/op
BenchmarkGoji_ParseParam                 1000000              1456 ns/op             336 B/op          2 allocs/op
BenchmarkGoJsonRest_ParseParam            200000             10412 ns/op            1712 B/op         33 allocs/op
BenchmarkGoRestful_ParseParam             200000              7994 ns/op             568 B/op         16 allocs/op
BenchmarkGorillaMux_ParseParam            500000              4492 ns/op             784 B/op          9 allocs/op
BenchmarkHttpRouter_ParseParam           5000000               251 ns/op              64 B/op          1 allocs/op
BenchmarkHttpTreeMux_ParseParam          2000000               911 ns/op             336 B/op          2 allocs/op
BenchmarkKocha_ParseParam                3000000               475 ns/op              56 B/op          3 allocs/op
BenchmarkMacaron_ParseParam               500000              4100 ns/op            1120 B/op         11 allocs/op
BenchmarkMartini_ParseParam               200000             10506 ns/op            1152 B/op         12 allocs/op
BenchmarkRevel_ParseParam                 200000              8457 ns/op            1704 B/op         28 allocs/op
BenchmarkRivet_ParseParam                1000000              1519 ns/op             464 B/op          5 allocs/op
BenchmarkPat_ParseParam                   500000              3926 ns/op            1136 B/op         20 allocs/op
BenchmarkTigerTonic_ParseParam            500000              4418 ns/op            1024 B/op         19 allocs/op
BenchmarkTraffic_ParseParam               200000              7665 ns/op            2280 B/op         25 allocs/op
BenchmarkVulcan_ParseParam               1000000              1125 ns/op              98 B/op          3 allocs/op
BenchmarkZeus_ParseParam                 1000000              3557 ns/op             864 B/op         16 allocs/op

BenchmarkAce_Parse2Params                5000000               381 ns/op              64 B/op          1 allocs/op
BenchmarkBeego_Parse2Params              1000000              3272 ns/op             784 B/op         11 allocs/op
BenchmarkBone_Parse2Params               1000000              2495 ns/op             736 B/op         10 allocs/op
BenchmarkDenco_Parse2Params              3000000               414 ns/op              64 B/op          1 allocs/op
BenchmarkGin_Parse2Params                5000000               385 ns/op              64 B/op          1 allocs/op
BenchmarkGocraftWeb_Parse2Params         1000000              2670 ns/op             720 B/op         10 allocs/op
BenchmarkGoji_Parse2Params               1000000              1428 ns/op             336 B/op          2 allocs/op
BenchmarkGoJsonRest_Parse2Params          200000             11545 ns/op            2064 B/op         36 allocs/op
BenchmarkGoRestful_Parse2Params           200000              8659 ns/op             568 B/op         16 allocs/op
BenchmarkGorillaMux_Parse2Params          300000              5257 ns/op             816 B/op          9 allocs/op
BenchmarkHttpRouter_Parse2Params         5000000               297 ns/op              64 B/op          1 allocs/op
BenchmarkHttpTreeMux_Parse2Params        1000000              1009 ns/op             336 B/op          2 allocs/op
BenchmarkKocha_Parse2Params              2000000               836 ns/op             128 B/op          5 allocs/op
BenchmarkMacaron_Parse2Params             500000              4443 ns/op            1168 B/op         12 allocs/op
BenchmarkMartini_Parse2Params             200000             10658 ns/op            1184 B/op         12 allocs/op
BenchmarkRevel_Parse2Params               200000              8833 ns/op            1768 B/op         30 allocs/op
BenchmarkRivet_Parse2Params              1000000              1737 ns/op             480 B/op          6 allocs/op
BenchmarkPat_Parse2Params                 500000              3843 ns/op             864 B/op         21 allocs/op
BenchmarkTigerTonic_Parse2Params          200000              6969 ns/op            1440 B/op         28 allocs/op
BenchmarkTraffic_Parse2Params             200000              7947 ns/op            2088 B/op         25 allocs/op
BenchmarkVulcan_Parse2Params             1000000              1396 ns/op              98 B/op          3 allocs/op
BenchmarkZeus_Parse2Params                500000              3938 ns/op             896 B/op         17 allocs/op

BenchmarkAce_ParseAll                     200000              8009 ns/op             640 B/op         16 allocs/op
BenchmarkBeego_ParseAll                    20000             70857 ns/op           15600 B/op        233 allocs/op
BenchmarkBone_ParseAll                     50000             35804 ns/op            8656 B/op        144 allocs/op
BenchmarkDenco_ParseAll                   200000              7701 ns/op             928 B/op         16 allocs/op
BenchmarkGin_ParseAll                     200000              8150 ns/op             640 B/op         16 allocs/op
BenchmarkGocraftWeb_ParseAll               30000             53824 ns/op           13936 B/op        207 allocs/op
BenchmarkGoji_ParseAll                     50000             28891 ns/op            5376 B/op         32 allocs/op
BenchmarkGoJsonRest_ParseAll               10000            257803 ns/op           39218 B/op        827 allocs/op
BenchmarkGoRestful_ParseAll                 5000            486378 ns/op           97312 B/op        904 allocs/op
BenchmarkGorillaMux_ParseAll               10000            165608 ns/op           17280 B/op        224 allocs/op
BenchmarkHttpRouter_ParseAll              300000              5375 ns/op             640 B/op         16 allocs/op
BenchmarkHttpTreeMux_ParseAll             100000             18532 ns/op            5376 B/op         32 allocs/op
BenchmarkKocha_ParseAll                   200000             12009 ns/op            1112 B/op         54 allocs/op
BenchmarkMacaron_ParseAll                  20000             98486 ns/op           25584 B/op        259 allocs/op
BenchmarkMartini_ParseAll                  10000            264803 ns/op           26848 B/op        302 allocs/op
BenchmarkRevel_ParseAll                    10000            210805 ns/op           40464 B/op        704 allocs/op
BenchmarkRivet_ParseAll                    50000             31548 ns/op            8592 B/op        103 allocs/op
BenchmarkPat_ParseAll                      20000             72594 ns/op           17568 B/op        382 allocs/op
BenchmarkTigerTonic_ParseAll               20000             97870 ns/op           20032 B/op        417 allocs/op
BenchmarkTraffic_ParseAll                  10000            242080 ns/op           69136 B/op        750 allocs/op
BenchmarkVulcan_ParseAll                   50000             36888 ns/op            2548 B/op         78 allocs/op
BenchmarkZeus_ParseAll                     10000            107571 ns/op           25504 B/op        463 allocs/op
```


### [GitHub](http://developer.github.com/v3/)

The GitHub API is rather large, consisting of 203 routes. The tasks are basically the same as in the benchmarks before.

```
BenchmarkAce_GithubStatic               10000000               169 ns/op               0 B/op          0 allocs/op
BenchmarkBeego_GithubStatic              1000000              1856 ns/op             368 B/op          7 allocs/op
BenchmarkBone_GithubStatic                100000             13747 ns/op            2880 B/op         60 allocs/op
BenchmarkDenco_GithubStatic             20000000              68.5 ns/op               0 B/op          0 allocs/op
BenchmarkGin_GithubStatic               10000000               188 ns/op               0 B/op          0 allocs/op
BenchmarkGocraftWeb_GithubStatic         1000000              1298 ns/op             304 B/op          6 allocs/op
BenchmarkGoji_GithubStatic               3000000               496 ns/op               0 B/op          0 allocs/op
BenchmarkGoRestful_GithubStatic            30000             51190 ns/op            2968 B/op         34 allocs/op
BenchmarkGoJsonRest_GithubStatic          200000              8264 ns/op            1080 B/op         29 allocs/op
BenchmarkGorillaMux_GithubStatic           50000             24870 ns/op             464 B/op          8 allocs/op
BenchmarkHttpRouter_GithubStatic        20000000              99.2 ns/op               0 B/op          0 allocs/op
BenchmarkHttpTreeMux_GithubStatic       20000000              86.1 ns/op               0 B/op          0 allocs/op
BenchmarkKocha_GithubStatic             20000000               105 ns/op               0 B/op          0 allocs/op
BenchmarkMacaron_GithubStatic            1000000              3010 ns/op             752 B/op          8 allocs/op
BenchmarkMartini_GithubStatic             100000             22996 ns/op             832 B/op         11 allocs/op
BenchmarkPat_GithubStatic                 100000             14131 ns/op            3648 B/op         76 allocs/op
BenchmarkRevel_GithubStatic               300000              7107 ns/op            1288 B/op         25 allocs/op
BenchmarkRivet_GithubStatic              3000000               563 ns/op             112 B/op          2 allocs/op
BenchmarkTigerTonic_GithubStatic         3000000               410 ns/op              48 B/op          1 allocs/op
BenchmarkTraffic_GithubStatic              20000             67309 ns/op           22792 B/op        167 allocs/op
BenchmarkVulcan_GithubStatic             1000000              1589 ns/op              98 B/op          3 allocs/op
BenchmarkZeus_GithubStatic                 50000             41160 ns/op            7664 B/op        140 allocs/op

BenchmarkAce_GithubParam                 3000000               553 ns/op              96 B/op          1 allocs/op
BenchmarkBeego_GithubParam                500000              3525 ns/op             784 B/op         11 allocs/op
BenchmarkBone_GithubParam                1000000              3403 ns/op             853 B/op         12 allocs/op
BenchmarkDenco_GithubParam               2000000               625 ns/op             128 B/op          1 allocs/op
BenchmarkGin_GithubParam                 3000000               560 ns/op              96 B/op          1 allocs/op
BenchmarkGocraftWeb_GithubParam          1000000              2770 ns/op             720 B/op         10 allocs/op
BenchmarkGoji_GithubParam                1000000              1917 ns/op             336 B/op          2 allocs/op
BenchmarkGoJsonRest_GithubParam           200000             11845 ns/op            2080 B/op         36 allocs/op
BenchmarkGoRestful_GithubParam             30000             43783 ns/op             568 B/op         16 allocs/op
BenchmarkGorillaMux_GithubParam           100000             14472 ns/op             816 B/op          9 allocs/op
BenchmarkHttpRouter_GithubParam          3000000               476 ns/op              96 B/op          1 allocs/op
BenchmarkHttpTreeMux_GithubParam         1000000              1190 ns/op             336 B/op          2 allocs/op
BenchmarkKocha_GithubParam               2000000               935 ns/op             128 B/op          5 allocs/op
BenchmarkMacaron_GithubParam              500000              4551 ns/op            1168 B/op         12 allocs/op
BenchmarkMartini_GithubParam               50000             31201 ns/op            1184 B/op         12 allocs/op
BenchmarkPat_GithubParam                  200000             10139 ns/op            2480 B/op         56 allocs/op
BenchmarkRevel_GithubParam                200000              9009 ns/op            1784 B/op         30 allocs/op
BenchmarkRivet_GithubParam               1000000              1910 ns/op             480 B/op          6 allocs/op
BenchmarkTigerTonic_GithubParam           200000              7003 ns/op            1440 B/op         28 allocs/op
BenchmarkTraffic_GithubParam               50000             25926 ns/op            6984 B/op         59 allocs/op
BenchmarkVulcan_GithubParam               500000              2508 ns/op              98 B/op          3 allocs/op
BenchmarkZeus_GithubParam                  50000             26529 ns/op            6448 B/op         96 allocs/op

BenchmarkAce_GithubAll                     10000            101042 ns/op           13792 B/op        167 allocs/op
BenchmarkBeego_GithubAll                    2000            702309 ns/op          146272 B/op       2092 allocs/op
BenchmarkBone_GithubAll                     1000           1699515 ns/op          360704 B/op       5370 allocs/op
BenchmarkDenco_GithubAll                   10000            111352 ns/op           20224 B/op        167 allocs/op
BenchmarkGin_GithubAll                     10000            104368 ns/op           13792 B/op        167 allocs/op
BenchmarkGocraftWeb_GithubAll               5000            524757 ns/op          133280 B/op       1889 allocs/op
BenchmarkGoji_GithubAll                     2000            981690 ns/op           56112 B/op        334 allocs/op
BenchmarkGoJsonRest_GithubAll               1000           2308087 ns/op          387716 B/op       7069 allocs/op
BenchmarkGoRestful_GithubAll                 100          11144575 ns/op          598108 B/op       7152 allocs/op
BenchmarkGorillaMux_GithubAll                200           8798084 ns/op          153137 B/op       1791 allocs/op
BenchmarkHttpRouter_GithubAll              20000             82522 ns/op           13792 B/op        167 allocs/op
BenchmarkHttpTreeMux_GithubAll             10000            208423 ns/op           56112 B/op        334 allocs/op
BenchmarkKocha_GithubAll                   10000            183138 ns/op           23304 B/op        843 allocs/op
BenchmarkMacaron_GithubAll                  2000            943212 ns/op          224960 B/op       2315 allocs/op
BenchmarkMartini_GithubAll                   100          13946836 ns/op          237952 B/op       2686 allocs/op
BenchmarkPat_GithubAll                       300           5622027 ns/op         1504101 B/op      32222 allocs/op
BenchmarkRevel_GithubAll                    1000           1800536 ns/op          345553 B/op       5918 allocs/op
BenchmarkRivet_GithubAll                   10000            334249 ns/op           84272 B/op       1079 allocs/op
BenchmarkTigerTonic_GithubAll               2000           1313607 ns/op          241088 B/op       6052 allocs/op
BenchmarkTraffic_GithubAll                   100          12054665 ns/op         3103197 B/op      24284 allocs/op
BenchmarkVulcan_GithubAll                   5000            402176 ns/op           19894 B/op        609 allocs/op
BenchmarkZeus_GithubAll                      200           9157014 ns/op         1925239 B/op      30938 allocs/op
```

### [Google+](https://developers.google.com/+/api/latest/)

Last but not least the Google+ API, consisting of 13 routes. In reality this is just a subset of a much larger API.

```
BenchmarkAce_GPlusStatic                10000000               123 ns/op               0 B/op          0 allocs/op
BenchmarkBeego_GPlusStatic               1000000              1673 ns/op             352 B/op          7 allocs/op
BenchmarkBone_GPlusStatic               10000000               212 ns/op              32 B/op          1 allocs/op
BenchmarkDenco_GPlusStatic              30000000              48.3 ns/op               0 B/op          0 allocs/op
BenchmarkGin_GPlusStatic                10000000               138 ns/op               0 B/op          0 allocs/op
BenchmarkGocraftWeb_GPlusStatic          1000000              1191 ns/op             288 B/op          6 allocs/op
BenchmarkGoji_GPlusStatic                5000000               352 ns/op               0 B/op          0 allocs/op
BenchmarkGoJsonRest_GPlusStatic           200000              8232 ns/op            1064 B/op         29 allocs/op
BenchmarkGoRestful_GPlusStatic            200000              9405 ns/op            1736 B/op         28 allocs/op
BenchmarkGorillaMux_GPlusStatic          1000000              2604 ns/op             464 B/op          8 allocs/op
BenchmarkHttpRouter_GPlusStatic         30000000              52.5 ns/op               0 B/op          0 allocs/op
BenchmarkHttpTreeMux_GPlusStatic        30000000              50.5 ns/op               0 B/op          0 allocs/op
BenchmarkKocha_GPlusStatic              20000000              76.5 ns/op               0 B/op          0 allocs/op
BenchmarkMacaron_GPlusStatic             1000000              2899 ns/op             736 B/op          8 allocs/op
BenchmarkMartini_GPlusStatic              300000              6046 ns/op             832 B/op         11 allocs/op
BenchmarkPat_GPlusStatic                 3000000               425 ns/op              96 B/op          2 allocs/op
BenchmarkRevel_GPlusStatic                300000              6823 ns/op            1272 B/op         25 allocs/op
BenchmarkRivet_GPlusStatic               3000000               510 ns/op             112 B/op          2 allocs/op
BenchmarkTigerTonic_GPlusStatic         10000000               233 ns/op              32 B/op          1 allocs/op
BenchmarkTraffic_GPlusStatic              300000              4939 ns/op            1480 B/op         19 allocs/op
BenchmarkVulcan_GPlusStatic              2000000               957 ns/op              98 B/op          3 allocs/op
BenchmarkZeus_GPlusStatic                3000000               429 ns/op              48 B/op          2 allocs/op

BenchmarkAce_GPlusParam                  5000000               385 ns/op              64 B/op          1 allocs/op
BenchmarkBeego_GPlusParam                1000000              2940 ns/op             720 B/op         10 allocs/op
BenchmarkBone_GPlusParam                 1000000              2452 ns/op             696 B/op         10 allocs/op
BenchmarkDenco_GPlusParam                5000000               359 ns/op              64 B/op          1 allocs/op
BenchmarkGin_GPlusParam                  5000000               392 ns/op              64 B/op          1 allocs/op
BenchmarkGocraftWeb_GPlusParam           1000000              2301 ns/op             656 B/op          9 allocs/op
BenchmarkGoji_GPlusParam                 1000000              1312 ns/op             336 B/op          2 allocs/op
BenchmarkGoJsonRest_GPlusParam            200000             10726 ns/op            1728 B/op         33 allocs/op
BenchmarkGoRestful_GPlusParam             200000              7841 ns/op             632 B/op         18 allocs/op
BenchmarkGorillaMux_GPlusParam            300000              5398 ns/op             784 B/op          9 allocs/op
BenchmarkHttpRouter_GPlusParam           5000000               294 ns/op              64 B/op          1 allocs/op
BenchmarkHttpTreeMux_GPlusParam          2000000               930 ns/op             336 B/op          2 allocs/op
BenchmarkKocha_GPlusParam                3000000               512 ns/op              56 B/op          3 allocs/op
BenchmarkMacaron_GPlusParam               500000              4077 ns/op            1104 B/op         11 allocs/op
BenchmarkMartini_GPlusParam               200000             11757 ns/op            1152 B/op         12 allocs/op
BenchmarkPat_GPlusParam                  1000000              2701 ns/op             704 B/op         14 allocs/op
BenchmarkRevel_GPlusParam                 200000              8374 ns/op            1704 B/op         28 allocs/op
BenchmarkRivet_GPlusParam                1000000              1557 ns/op             464 B/op          5 allocs/op
BenchmarkTigerTonic_GPlusParam            500000              4575 ns/op            1064 B/op         19 allocs/op
BenchmarkTraffic_GPlusParam               200000              7626 ns/op            2000 B/op         23 allocs/op
BenchmarkVulcan_GPlusParam               1000000              1410 ns/op              98 B/op          3 allocs/op
BenchmarkZeus_GPlusParam                 1000000              2935 ns/op             752 B/op         13 allocs/op

BenchmarkAce_GPlus2Params                3000000               447 ns/op              64 B/op          1 allocs/op
BenchmarkBeego_GPlus2Params               500000              3755 ns/op             784 B/op         11 allocs/op
BenchmarkBone_GPlus2Params                500000              3669 ns/op             896 B/op         12 allocs/op
BenchmarkDenco_GPlus2Params              3000000               495 ns/op              64 B/op          1 allocs/op
BenchmarkGin_GPlus2Params                3000000               453 ns/op              64 B/op          1 allocs/op
BenchmarkGocraftWeb_GPlus2Params         1000000              2771 ns/op             720 B/op         10 allocs/op
BenchmarkGoji_GPlus2Params               1000000              1802 ns/op             336 B/op          2 allocs/op
BenchmarkGoJsonRest_GPlus2Params          200000             12098 ns/op            2096 B/op         36 allocs/op
BenchmarkGoRestful_GPlus2Params           200000              9790 ns/op             632 B/op         18 allocs/op
BenchmarkGorillaMux_GPlus2Params          200000             11349 ns/op             816 B/op          9 allocs/op
BenchmarkHttpRouter_GPlus2Params         5000000               358 ns/op              64 B/op          1 allocs/op
BenchmarkHttpTreeMux_GPlus2Params        1000000              1116 ns/op             336 B/op          2 allocs/op
BenchmarkKocha_GPlus2Params              2000000               939 ns/op             128 B/op          5 allocs/op
BenchmarkMacaron_GPlus2Params             500000              4473 ns/op            1168 B/op         12 allocs/op
BenchmarkMartini_GPlus2Params              50000             38068 ns/op            1280 B/op         16 allocs/op
BenchmarkPat_GPlus2Params                 200000              8489 ns/op            2304 B/op         41 allocs/op
BenchmarkRevel_GPlus2Params               200000              9014 ns/op            1800 B/op         30 allocs/op
BenchmarkRivet_GPlus2Params              1000000              1777 ns/op             480 B/op          6 allocs/op
BenchmarkTigerTonic_GPlus2Params          200000              7387 ns/op            1528 B/op         28 allocs/op
BenchmarkTraffic_GPlus2Params             100000             17274 ns/op            3552 B/op         35 allocs/op
BenchmarkVulcan_GPlus2Params             1000000              2053 ns/op              98 B/op          3 allocs/op
BenchmarkZeus_GPlus2Params                200000             11579 ns/op            2680 B/op         41 allocs/op

BenchmarkAce_GPlusAll                     300000              4996 ns/op             640 B/op         11 allocs/op
BenchmarkBeego_GPlusAll                    30000             39325 ns/op            8976 B/op        129 allocs/op
BenchmarkBone_GPlusAll                    100000             18105 ns/op            3472 B/op         59 allocs/op
BenchmarkDenco_GPlusAll                   300000              5277 ns/op             672 B/op         11 allocs/op
BenchmarkGin_GPlusAll                     300000              5110 ns/op             640 B/op         11 allocs/op
BenchmarkGocraftWeb_GPlusAll               50000             30943 ns/op            8144 B/op        116 allocs/op
BenchmarkGoji_GPlusAll                    100000             18565 ns/op            3696 B/op         22 allocs/op
BenchmarkGoJsonRest_GPlusAll               10000            143977 ns/op           22849 B/op        436 allocs/op
BenchmarkGoRestful_GPlusAll                10000            170441 ns/op           26264 B/op        404 allocs/op
BenchmarkGorillaMux_GPlusAll               20000             87191 ns/op            9712 B/op        115 allocs/op
BenchmarkHttpRouter_GPlusAll              500000              3774 ns/op             640 B/op         11 allocs/op
BenchmarkHttpTreeMux_GPlusAll             200000             11488 ns/op            3696 B/op         22 allocs/op
BenchmarkKocha_GPlusAll                   200000              8773 ns/op             976 B/op         43 allocs/op
BenchmarkMacaron_GPlusAll                  30000             53987 ns/op           13968 B/op        142 allocs/op
BenchmarkMartini_GPlusAll                  10000            210862 ns/op           15072 B/op        178 allocs/op
BenchmarkPat_GPlusAll                      20000             66910 ns/op           16880 B/op        343 allocs/op
BenchmarkRevel_GPlusAll                    10000            110125 ns/op           21656 B/op        368 allocs/op
BenchmarkRivet_GPlusAll                   100000             19947 ns/op            5408 B/op         64 allocs/op
BenchmarkTigerTonic_GPlusAll               20000             72119 ns/op           14800 B/op        320 allocs/op
BenchmarkTraffic_GPlusAll                  10000            156004 ns/op           41184 B/op        439 allocs/op
BenchmarkVulcan_GPlusAll                  100000             19854 ns/op            1274 B/op         39 allocs/op
BenchmarkZeus_GPlusAll                     20000             90316 ns/op           19024 B/op        352 allocs/op
```


## Conclusions
First of all, there is no reason to use net/http's default [ServeMux](http://golang.org/pkg/net/http/#ServeMux), which is very limited and does not have especially good performance. There are enough alternatives coming in every flavor, choose the one you like best.

Secondly, the broad range of functions of some of the frameworks comes at a high price in terms of performance. For example Martini has great flexibility, but very bad performance. Martini has the worst performance of all tested routers in a lot of the benchmarks. Beego seems to have some scalability problems and easily defeats Martini with even worse performance, when the number of parameters or routes is high. I really hope, that the routing of these packages can be optimized. I think the Go-ecosystem needs great feature-rich frameworks like these.

Last but not least, we have to determine the performance champion.

Denco and its predecessor Kocha-urlrouter seem to have great performance, but are not convenient to use as a router for the net/http package. A lot of extra work is necessary to use it as a http.Handler. [The README of Denco claims](https://github.com/naoina/denco/blob/b03dbb499269a597afd0db715d408ebba1329d04/README.md), that the package is not intended as a replacement for [http.ServeMux](http://golang.org/pkg/net/http/#ServeMux).

[Goji](https://github.com/zenazn/goji/) looks very decent. It has great performance while also having a great range of features, more than any other router / framework in the top group.

Currently no router can beat the performance of the [HttpRouter](https://github.com/julienschmidt/httprouter) package, which currently dominates nearly all benchmarks.

In the end, performance can not be the (only) criterion for choosing a router. Play around a bit with some of the routers, and choose the one you like best.
