树形查询结果

AreaMapper.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.wayne.sparrow.app.mapper.area.AreaMapper">
    <resultMap id="BaseResultMap" type="com.wayne.sparrow.core.common.bojo.area.Area">
        <id column="area_id" jdbcType="BIGINT" property="id"/>
        <result column="name" jdbcType="VARCHAR" property="name"/>
        <result column="code" jdbcType="VARCHAR" property="code"/>
        <result column="level" jdbcType="INTEGER" property="level"/>
        <result column="sort" jdbcType="INTEGER" property="sort"/>
        <result column="area_pid" jdbcType="BIGINT" property="pid"/>
        <collection property="subArea" ofType="com.wayne.sparrow.core.common.bojo.area.Area"
                    column="area_id" javaType="java.util.ArrayList" select="selectSubArea"/>
    </resultMap>
    <resultMap id="SubResultMap" type="com.wayne.sparrow.core.common.bojo.area.Area">
        <id column="area_id" jdbcType="BIGINT" property="id"/>
        <result column="name" jdbcType="VARCHAR" property="name"/>
        <result column="code" jdbcType="VARCHAR" property="code"/>
        <result column="level" jdbcType="INTEGER" property="level"/>
        <result column="sort" jdbcType="INTEGER" property="sort"/>
        <result column="area_pid" jdbcType="BIGINT" property="pid"/>
        <collection property="subArea" ofType="com.wayne.sparrow.core.common.bojo.area.Area"
                    column="area_id" javaType="java.util.ArrayList" select="selectSubArea"/>
    </resultMap>

    <sql id="table_name">
        area
    </sql>
    <sql id="Base_Column_List">
        area_id, code, "name", "level", "sort", area_pid
    </sql>

    <select id="findById" parameterType="java.lang.Long" resultMap="BaseResultMap">
        select
        <include refid="Base_Column_List"/>
        from
        <include refid="table_name"/>
        where area_id = #{id,jdbcType=BIGINT}
    </select>

    <select id="findTreeById" parameterType="java.lang.Long" resultMap="BaseResultMap">
        select
        <include refid="Base_Column_List"/>
        from
        <include refid="table_name"/>
        where area_id = #{id,jdbcType=BIGINT}
    </select>

    <select id="selectSubArea" parameterType="java.lang.Long" resultMap="SubResultMap">
        select
        <include refid="Base_Column_List"/>
        from
        <include refid="table_name"/>
        WHERE area_pid = #{area_id}
    </select>
</mapper>
```

AreaController.java

```
import com.wayne.sparrow.app.mapper.area.AreaMapper;
import com.wayne.sparrow.core.common.bojo.area.Area;
import com.wayne.sparrow.core.common.controller.BaseController;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.ArrayList;
import java.util.List;

@Controller
@RequestMapping("/area")
public class AreaController extends BaseController{

    @Autowired
    private AreaMapper areaMapper;

    @RequestMapping("/demo")
    public String test() {
        return "area/demo";
    }

    @RequestMapping("/getById")
    @ResponseBody
    public Area getById(@RequestParam(name = "id", defaultValue = "0") Long id) {
        Area p = areaMapper.getById(id);
        return p;
    }

    @RequestMapping("/findById")
    @ResponseBody
    public Area findById(@RequestParam(name = "id", defaultValue = "0") Long id) {
        Area p = areaMapper.findTreeById(id);
        return p;
    }
}
```

实体类：Area.java

```
package com.wayne.sparrow.core.common.bojo.area;

import lombok.Data;

import java.util.List;

@Data
public class Area {
    Long id ;
    String name;
    String code; // 行政代码
//    int level; // 行政划分层级
//    int sort; // 排序
    Long pid;
    List<Area> subArea;
}
```

数据库表area：

```
DROP TABLE IF EXISTS area;
CREATE TABLE area
(
  area_id      BIGSERIAL NOT NULL,
  code         CHARACTER VARYING(10),
  name         CHARACTER VARYING(30),
  level        INTEGER,
  sort         INTEGER,
  area_pid     BIGINT,
  status       SMALLINT,
  date_modify  TIMESTAMP(3) WITHOUT TIME ZONE,
  date_created TIMESTAMP(3) WITHOUT TIME ZONE DEFAULT now(),
  CONSTRAINT area_pkey PRIMARY KEY (area_id)
);
```

web 访问： localhost/area/findById?id=1

