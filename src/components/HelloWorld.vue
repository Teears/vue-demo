<template>
  <div class="hello">
    <div class="myChart" id="myChart" ref="myChart"></div>
  </div>
</template>

<script>
import china from "echarts/map/json/china.json";
import world from "echarts/map/json/world.json";
export default {
  data() {
    return {
      option: {
        geo: {
          map: "world",
          type: "map",
          center: [114.07, 22.62],
          boundingCoords: [
            [64, 45],
            [120, -20]
          ],
          aspectScale: 1,
          selectedMode: "single",
          label: {
            normal: {
              show: false
            },
            emphasis: {
              show: false
            }
          },
          itemStyle: {
            normal: {
              borderWidth: 1,
              borderColor: "#69b0fe",
              areaColor: "#04388c",
              label: {
                show: false
              }
            },
            emphasis: {
              borderWidth: 1,
              borderColor: "#69b0fe",
              areaColor: "#04388c",
              label: {
                show: false
              }
            }
          },
          regions: [
            {
              name: "四川",
              itemStyle: {
                normal: {
                  borderWidth: 1,
                  borderColor: "#2affeb",
                  areaColor: "#05365f",
                  label: {
                    show: false
                  }
                },
                emphasis: {
                  borderWidth: 2,
                  borderColor: "#ffffff",
                  areaColor: "#04388c",
                  shadowColor: "#04aaff",
                  shadowBlur: 20,
                  label: {
                    show: false
                  }
                }
              }
            },
            {
              name: "广东",
              itemStyle: {
                normal: {
                  borderWidth: 1,
                  borderColor: "#2affeb",
                  areaColor: "#05365f",
                  label: {
                    show: false
                  }
                },
                emphasis: {
                  borderWidth: 2,
                  borderColor: "#ffffff",
                  areaColor: "#04388c",
                  shadowColor: "#04aaff",
                  shadowBlur: 20,
                  label: {
                    show: false
                  }
                }
              }
            },
            {
              name: "Malaysia",
              itemStyle: {
                normal: {
                  borderWidth: 1,
                  borderColor: "#2affeb",
                  areaColor: "#05365f",
                  label: {
                    show: false
                  }
                },
                emphasis: {
                  borderWidth: 2,
                  borderColor: "#ffffff",
                  areaColor: "#04388c",
                  shadowColor: "#04aaff",
                  shadowBlur: 20,
                  label: {
                    show: false
                  }
                }
              }
            },
            {
              name: "Vietnam",
              itemStyle: {
                normal: {
                  borderWidth: 1,
                  borderColor: "#2affeb",
                  areaColor: "#05365f",
                  label: {
                    show: false
                  }
                },
                emphasis: {
                  borderWidth: 2,
                  borderColor: "#ffffff",
                  areaColor: "#04388c",
                  shadowColor: "#04aaff",
                  shadowBlur: 20,
                  label: {
                    show: false
                  }
                }
              }
            }
          ]
        },
        series: {
          type: "scatter",
          coordinateSystem: "geo",
          symbol: "../../static/po.png",
          itemStyle: {
            color: "#b02a02"
          },
          data: [
            [104.06, 30.67, 50],
            [114.07,22.62, 50],
          ]
        }
      }
    };
  },
  methods: {
    decodePolygon(coordinate, encodeOffsets, encodeScale) {
      var result = [];
      var prevX = encodeOffsets[0];
      var prevY = encodeOffsets[1];
      for (var i = 0; i < coordinate.length; i += 2) {
        var x = coordinate.charCodeAt(i) - 64;
        var y = coordinate.charCodeAt(i + 1) - 64;
        // ZigZag decoding
        x = (x >> 1) ^ -(x & 1);
        y = (y >> 1) ^ -(y & 1);
        // Delta deocding
        x += prevX;
        y += prevY;
        prevX = x;
        prevY = y;
        // Dequantize
        result.push([x / encodeScale, y / encodeScale]);
      }
      return result;
    },
    decode(json) {
      if (!json.UTF8Encoding) {
        return json;
      }
      var encodeScale = json.UTF8Scale;
      if (encodeScale == null) {
        encodeScale = 1024;
      }
      var features = json.features;
      for (var f = 0; f < features.length; f++) {
        var feature = features[f];
        var geometry = feature.geometry;
        var coordinates = geometry.coordinates;
        var encodeOffsets = geometry.encodeOffsets;
        for (var c = 0; c < coordinates.length; c++) {
          var coordinate = coordinates[c];
          if (geometry.type === "Polygon") {
            coordinates[c] = this.decodePolygon(
              coordinate,
              encodeOffsets[c],
              encodeScale
            );
          } else if (geometry.type === "MultiPolygon") {
            for (var c2 = 0; c2 < coordinate.length; c2++) {
              var polygon = coordinate[c2];
              coordinate[c2] = this.decodePolygon(
                polygon,
                encodeOffsets[c][c2],
                encodeScale
              );
            }
          }
        }
      }
      // Has been decoded
      json.UTF8Encoding = false;
      return json;
    }
  },
  mounted() {
    let data = this.decode(china);
    console.log(world);
    console.log(data);
    let worldAndChina = Object.assign({}, world); // 原本的world还需要用 所以这里用了一个深拷贝赋值世界地图数据
    worldAndChina.features = worldAndChina.features.concat(data.features); // 对，就是这么简单用concat把两者的features合并起来就可以了
    var chart = this.$echarts.init(this.$refs.myChart);
    this.$echarts.registerMap("world", worldAndChina);
    chart.setOption(this.option);
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello {
  height: 100%;
  width: 100%;
}
.myChart {
  height: 100%;
  width: 100%;
}
</style>
