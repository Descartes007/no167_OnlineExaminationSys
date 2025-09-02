<!--
 * 成绩统计页面
 *
 * @Author: ShanZhu
 * @Date: 2023-11-23
-->
<template>
  <div id="grade">
    <div ref="box" class="box"></div>
    <div class="notFound" v-if="isNull">
      <i class="iconfont icon-LC_icon_tips_fill"></i
      ><span>该考生未参加考试</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "grade",
  data() {
    return {
      isNull: false, //原始数据
      tableDataX: [], //x轴数据 保存次数
      tableDataY: [], //y轴数据 保存分数
    };
  },
  mounted() {
    this.score();
  },
  methods: {
    score() {
      let studentId = this.$route.query.studentId;
      this.$axios(`/api/score/${studentId}`).then((res) => {
        //根据学生Id查询成绩
        if (res.data.code == 200) {
          let rootData = res.data.data;
          rootData.forEach((element, index) => {

            // 科目名称转列显示
            var insertIntervalString = (
              originStr,
              disNum = 10,
              insertStr = "\n"
            ) =>
              originStr.replace(
                new RegExp("(.{" + disNum + "})", "g"),
                "$1" + insertStr
              );
            // 每隔一个字符插入一个回车
            var subject = insertIntervalString(element.subject, 3, "\n");

            this.tableDataX.push(subject);
            this.tableDataY.push(element.etScore);
          });
          let boxDom = this.$refs["box"];
          let scoreCharts = this.$echarts.init(boxDom);
          let option = {
            xAxis: {
              type: "category",
              data: this.tableDataX,
            },
            yAxis: {
              type: "value",
            },
            series: [
              {
                data: this.tableDataY,
                type: "line",
                itemStyle: { normal: { label: { show: true } } },
              },
            ],
          };
          scoreCharts.setOption(option);
          scoreCharts.on("mouseover", (params) => {
            console.log(params.value);
          });
        } else {
          this.isNull = true;
        }
      });
    },
  },
};
</script>

<style lang="less" scoped>
#grade {
  position: relative;
  .box {
    width: 600px;
    height: 400px;
  }
  .notFound {
    position: absolute;
    top: 0px;
    left: 0px;
  }
}
</style>
