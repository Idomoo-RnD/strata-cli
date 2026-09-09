# Runnable scenes — generated from tested fixtures

Complete compact scenes, not fragments. Copy the chosen JSON to a project. For text examples,
copy a licensed font that covers the copy to `font.ttf` beside the scene; use `strata glyphs`
before compiling. The offline tests use the repository DejaVuSans font under its included license.
No asset synthesis or cloud access is needed to compile. Cloud visual proofs are separately pending.

```bash
strata validate example.json
strata compile example.json -o example.idm
strata preview example.json --at 1 --grid
# Only after approval of the library and spend:
strata snapshot example.json --library <approved-id> --at 1
```

See [case studies](../craft/case-studies.md) for interpretation and [recipes](../recipes.md) for fragments.

## typewriter

Proof: offline selection/compile; rendered glyph proof pending. Source: [typewriter.json](typewriter.json).

```json
{
  "width": 1280,
  "height": 720,
  "fps": 25,
  "duration": 4,
  "layers": [
    {
      "type": "solid",
      "name": "paper",
      "color": "#15251f",
      "box": [
        0,
        0,
        1280,
        720
      ]
    },
    {
      "type": "text",
      "name": "typed_title",
      "text": "TYPE",
      "font": "./font.ttf",
      "size": 120,
      "color": "#f2eee3",
      "box": [
        180,
        240,
        920,
        180
      ],
      "align": "left middle",
      "animators": [
        {
          "opacity": 0,
          "ranges": [
            {
              "based_on": "characters",
              "shape": "square",
              "end": 1,
              "animate": {
                "start": [
                  {
                    "t": 0,
                    "v": 0,
                    "ease": "hold"
                  },
                  {
                    "t": 0.4,
                    "v": 0.25,
                    "ease": "hold"
                  },
                  {
                    "t": 0.8,
                    "v": 0.5,
                    "ease": "hold"
                  },
                  {
                    "t": 1.2,
                    "v": 0.75,
                    "ease": "hold"
                  },
                  {
                    "t": 1.6,
                    "v": 1
                  }
                ]
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## flip-reveal

Proof: offline schema and pinned selection; rendered flip proof pending. Source: [flip-reveal.json](flip-reveal.json).

```json
{
  "width": 1280,
  "height": 720,
  "fps": 25,
  "duration": 4,
  "layers": [
    {
      "type": "solid",
      "name": "flip_ground",
      "color": "#15251f",
      "box": [
        0,
        0,
        1280,
        720
      ]
    },
    {
      "type": "text",
      "name": "flip_title",
      "text": "ROTATE",
      "font": "./font.ttf",
      "size": 120,
      "color": "#f2eee3",
      "box": [
        140,
        240,
        1000,
        180
      ],
      "align": "center middle",
      "anchor": [
        640,
        330
      ],
      "motion_blur": true,
      "animators": [
        {
          "opacity": 0,
          "rotation": [
            0,
            90,
            0
          ],
          "ranges": [
            {
              "based_on": "characters",
              "shape": "square",
              "end": 1,
              "animate": {
                "start": [
                  {
                    "t": 0,
                    "v": 0,
                    "ease": "outCubic"
                  },
                  {
                    "t": 1.6,
                    "v": 1
                  }
                ]
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## path-reveal

Proof: offline path bake and compile; cloud finish proof pending. Source: [path-reveal.json](path-reveal.json).

```json
{
  "width": 1280,
  "height": 720,
  "fps": 25,
  "duration": 4,
  "layers": [
    {
      "type": "solid",
      "name": "path_ground",
      "color": "#15251f",
      "box": [
        0,
        0,
        1280,
        720
      ]
    },
    {
      "type": "solid",
      "name": "journey",
      "color": "#f2eee3",
      "box": [
        0,
        0,
        1280,
        720
      ],
      "mask": {
        "path": "M180 510 C400 140 800 640 1100 240",
        "stroke": 16,
        "cap": "round",
        "trim": {
          "end": [
            {
              "t": 0.4,
              "v": 0,
              "ease": "outCubic"
            },
            {
              "t": 2.4,
              "v": 1
            }
          ]
        }
      }
    }
  ]
}
```

## group-reveal

Proof: offline subcomp/transform compile; cloud finish proof pending. Source: [group-reveal.json](group-reveal.json).

```json
{
  "width": 1280,
  "height": 720,
  "fps": 25,
  "duration": 4,
  "comps": {
    "module": {
      "width": 520,
      "height": 200,
      "duration": 4,
      "layers": [
        {
          "type": "solid",
          "name": "module_panel",
          "color": "#f2eee3",
          "box": [
            0,
            0,
            520,
            200
          ]
        },
        {
          "type": "solid",
          "name": "module_marker",
          "color": "#176345",
          "box": [
            40,
            60,
            80,
            80
          ]
        },
        {
          "type": "solid",
          "name": "module_rule",
          "color": "#176345",
          "box": [
            160,
            90,
            280,
            20
          ]
        }
      ]
    }
  },
  "layers": [
    {
      "type": "solid",
      "name": "group_ground",
      "color": "#15251f",
      "box": [
        0,
        0,
        1280,
        720
      ]
    },
    {
      "type": "comp",
      "name": "group_instance",
      "comp": "module",
      "box": [
        380,
        260,
        520,
        200
      ],
      "duration": 4,
      "animate": {
        "position": [
          {
            "t": 0,
            "v": [
              0,
              100
            ],
            "ease": "outCubic"
          },
          {
            "t": 0.8,
            "v": [
              0,
              0
            ]
          }
        ]
      }
    }
  ]
}
```

## camera-parallax

Proof: offline schema/coordinates; projected render proof pending. Source: [camera-parallax.json](camera-parallax.json).

```json
{
  "width": 1280,
  "height": 720,
  "fps": 25,
  "duration": 4,
  "layers": [
    {
      "type": "solid",
      "name": "camera_ground",
      "color": "#15251f",
      "box": [
        0,
        0,
        1280,
        720
      ]
    },
    {
      "type": "solid",
      "name": "far_panel",
      "color": "#176345",
      "box": [
        260,
        180,
        300,
        360
      ],
      "is_3d": true,
      "position": [
        0,
        0,
        220
      ]
    },
    {
      "type": "solid",
      "name": "near_panel",
      "color": "#f2eee3",
      "box": [
        680,
        240,
        260,
        240
      ],
      "is_3d": true,
      "position": [
        0,
        0,
        -80
      ]
    },
    {
      "type": "camera",
      "name": "parallax_camera",
      "fov": 60,
      "position": [
        640,
        360,
        -623.538
      ],
      "motion_blur": true,
      "animate": {
        "position": [
          {
            "t": 0,
            "v": [
              600,
              360,
              -623.538
            ],
            "ease": "inOutSine"
          },
          {
            "t": 4,
            "v": [
              680,
              360,
              -623.538
            ]
          }
        ]
      }
    }
  ]
}
```
