<template>
<div style="min-height: 660px; cursor: auto;" class="edit">
  <div class="navbar navbar-inverse navbar-fixed-top">
    <div class="navbar-inner">
      <div class="container-fluid">
        <a class="brand">
          <img src="static/favicon.png" style="margin: 6px;"> 表单构建器 v0.0.1
          <span class="label">Beta</span>
        </a>
        <div class="nav-collapse collapse operate-menu">
          <ul class="nav" id="menu-layoutit" style="margin-top: 1rem;">
            <li class="divider-vertical"></li>
            <li>
              <el-button-group>
                <el-button type="primary" @click="edit"><i class="icon-edit icon-white"></i>编辑</el-button>
                <el-button type="primary" @click="sourcePreview"><i class="icon-eye-open icon-white"></i>预览</el-button>
              </el-button-group>
              <el-button-group>
                <el-button type="warning" @click="saveHTML"><i class="icon-share icon-white"></i>保存</el-button>
                <el-button type="warning" @click="clearHTML"><i class="icon-trash icon-white"></i>清空</el-button>
              </el-button-group>
              <el-button-group>
                <el-button type="info" @click="undo"><i class="icon-arrow-left icon-white"></i>撤销</el-button>
                <el-button type="info" @click="redo"><i class="icon-arrow-right icon-white"></i>重做</el-button>
              </el-button-group>
              <el-button-group>
                <el-button type="danger" @click="addRow" round><i class="el-icon-plus"></i>增加行</el-button>
              </el-button-group>
            </li>
          </ul>
          <div style="float: right; margin: 1rem;">
            <el-button type="success" @click="PublishForm" round><i class="el-icon-success"></i>发布业务表单</el-button>
          </div>
          
        </div>
        <!--/.nav-collapse -->
      </div>
    </div>
  </div>
  <div class="container-fix">
    <div class="row-fluid" style="min-height: fill-available;">
      <div class="">
        <div class="sidebar-nav left">
          <ul v-for="(item, index) in components" :key="item.id" class="nav nav-list accordion-group">
            <li class="nav-header" @click="showComponents">
              <i :class="item.icon"></i>{{item.label}}
              <div class="pull-right popover-info">
                <i class="icon-question-sign "></i>
                <div class="popover fade right">
                  <div class="arrow"></div>
                  <h3 class="popover-title">{{item.descriptionTitle}}</h3>
                  <div class="popover-content">{{item.description}}
                  </div>
                </div>
              </div>
            </li>
            <li class="boxes">
              <div v-for="(item_a, index_a) in item.list" :key="item_a.id" class="lyrow ui-draggable">
                <a class="remove label label-important" style="padding: 0;float: right;">
                  <i class="icon-remove icon-white"></i></a>
                <span class="drag label" style="display: inline-block; float: left; margin-left: 5px; position: relative; right: 0;">
                  <i :class="item_a.icon"></i> {{item_a.label}} <i class="icon-move"></i></span>
                <span class="configuration" style="margin-left: 1rem;">
                  <!-- <button v-for="(item_a_a, index_a_a) in item_a.buttons" type="button" class="btn btn-mini" data-target="#editorModal" role="button" @click="onClickComponentButton(item_a_a)" @click="onClickComponentButton(item_a_a)">{{item_a_a.label}}</button> -->
                  <a v-for="(item_a_a, index_a_a) in item_a.buttons" :key="item_a_a.id" type="button" class="btn btn-mini" style="padding: 0;">
                    <i v-if="item_a_a.id==='setting'" class="el-icon-setting"></i>
                    <i v-if="item_a_a.id!=='setting'">{{item_a_a.label}}</i></a>
                <!-- </span> -->
                  <span v-for="(item_a_b, index_a_b) in item_a.menus" :key="item_a_b.id" class="btn-group" style="width:auto;">
                    <div class="btn btn-mini dropdown-toggle" data-toggle="dropdown" >
                      {{item_a_b.label}}<span class="caret"></span>
                    </div>
                    <ul class="dropdown-menu">
                      <li v-for="(item_a_b_a, index_a_b_a) in item_a_b.elems" :key="item_a_b_a.id" class="active" @click="onClickComponentButton(item_a_b_a, item_a, $event)">
                        <a>{{item_a_b_a}}</a>
                      </li>
                    </ul>
                  </span>
                </span>
                <div class="preview"></div>
                <div class="view" :ref="item_a.id">
                  <div class="vue-wrapper">
                    <div v-html="item_a.html"></div>
                  </div>
                </div>
              </div>
            </li>
          </ul>
        </div>
      </div>
      <!--/span-->
      <div class="canvas ui-sortable">
        <div class="lyrow ui-draggable">
          <!-- <a href="#close" class="remove label label-important">
            <i class="icon-remove icon-white"></i>删除
            </a>
          <span class="drag label">
            <i class="icon-move"></i>拖拽</span> -->
          <div class="preview"></div>
          <div class="view">
            <!-- <div class="row-fluid clearfix"> -->
              <div class="span12 canvas-title row-container ui-sortable" style="min-height: -webkit-fill-available">
              </div>
            <!-- </div> -->
          </div>
        </div>
        <div style="
        position: relative;
        width: 100%;
        right: auto;
        bottom: auto;
        left: 0px;
        top: 0px;
        display: inline-block;
        opacity: 1;
        height: auto;">
        </div>
      </div>
      <!-- end canvas -->
      
      <div class="hide" id="set_properties">
        <div class="sidebar-nav sidebar-nav-right" >
          <ul v-for="(item, index) in property" :key="item.id" class="nav nav-list accordion-group">
            <li>
              <!-- <div v-for="(item_a, index_a) in item.list" :key="item_a.id"> -->
                
                <div v-if="'el-settings' == item.id" class="row" id="addProperty">
                  <div class="col-sm-12" @click="addProperty"><i class="icon-plus"></i><label>{{item.label}}</label></div>
                </div>
                <div v-else-if="'el-class' == item.id || 'el-style' == item.id" class="row">
                  <div class="col-sm-4"><label :name="item.label">{{item.label}}:</label></div>
                  <div class="col-sm-8"><textarea :placeholder="item.placeholder" class="inputprop"></textarea></div>
                </div>
                <div v-else-if="'el-array' == item.id">
                  <div v-for="(item_a, index_a) in item" :key="index_a">
                    <div v-if="'id' != index_a">
                      <div><label :name="index_a">{{index_a}}</label></div>
                      <div class="row">
                        <div v-for="(item_b, index_b) in item_a" :key="index_b">
                          <div v-if="'string' == item_b.placeholder || 'number' == item_b.placeholder">
                            <div class="col-sm-4"><label :name="item_b.label">{{item_b.label}}:</label></div>
                            <div class="col-sm-8"><input type="text" :placeholder="item_b.placeholder" class="inputprop"/></div>
                          </div>
                          <div v-else-if="'boolean' == item_b.placeholder">
                            <div class="col-sm-12"><label :name="item_b.label">{{item_b.label}}:</label></div>
                            <div class="col-sm-12">是：<input type="radio" :name="index_b" class="inputprop" value="y"/>   否：<input type="radio" :name="index_b" checked="checked" class="inputprop" value="n"/></div>
                          </div>
                          <div v-else>
                            <div class="col-sm-4"><label :name="item_b.label">{{item_b.label}}:</label></div>
                            <div class="col-sm-8"><textarea :placeholder="item_b.placeholder" class="inputprop"></textarea></div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
                <div v-else class="row">
                  <div class="col-sm-4"><label :name="item.label">{{item.label}}:</label></div>
                  <div class="col-sm-8"><input type="text" :placeholder="item.placeholder" class="inputprop"/></div>
                </div>
              <!-- </div> -->
            </li>
          </ul>
        </div>
      </div>

      <!--/span-->
      <div id="download-layout">
        <div class="container-fluid"></div>
      </div>
    </div>
    <!--/row-->
  </div>
  <!--/.fluid-container-->
</div>
</template>

<script>
import { mapGetters } from "vuex";
import engine from "./form.designer.engine";
import components from "./components.menu.config";
import * as properties from "./components.properties.config";

let currenteditor;

export default {
  name: "form-designer",
  data() {
    return {
      // showName: "basic",
      stopsave: 0,
      startdrag: 0,
      components,
      properties,
      property: [],
      params: {
        createdBy: 'test user',
        creationDate: '',
        cssfiles: "",
        formTemplateGroupId: 0,
        html: '',
        id: -1,
        jsfiles: "",
        lastUpdateDate: '',
        lastUpdatedBy: 'test user',
        remark: 'test',
        status: 0,
        version: ''
      },
      	// <el-main style="height: -webkit-fill-available;"></el-main>
      formDom: $(`
				<el-main style="line-height: 50px; overflow-y: scroll;"></el-main>
        `),
      selectTag: {
        id: '',
        tagName: ''
      }
    };
  },
  computed: {
    ...mapGetters(["appContextPath"])
  },
  methods: {
    ...engine
  },
  created() {
    $(window).resize(function() {
      $("body").css("min-height", $(window).height() - 90);
      $(".canvas").css("min-height", $(window).height() - 160);
    });
  },
  mounted() {
    const scope = this;
    /**
 * 禁用ckeditor
 * CKEDITOR.disableAutoInline = true;
 *  var contenthandle = CKEDITOR.replace( 'contenteditor' ,{
 * language: 'zh-cn',
 * contentsCss: ['css/bootstrap-combined.min.css'],
 * allowedContent: true
 * });
 * var eText = currenteditor.html();
 * contenthandle.setData(eText);
 * currenteditor.html(contenthandle.getData());
 */
    this.components.forEach(e => {
      e.list &&
        e.list.forEach(el => {
          const ref = scope.$refs[el.id];
        });
    });
    /* this.properties.forEach(e => {
      e.list &&
        e.list.forEach(el => {
          const ref = scope.$refs[el.id];
        });
    }); */
    this.restoreData();
    this.initContainer();
    $("body").css("min-height", $(window).height() - 90);
    $(".canvas").css("min-height", $(window).height() - 160);
    $(".sidebar-nav .lyrow").draggable({
      connectToSortable: ".canvas",
      helper: "clone",
      handle: ".drag",
      start: function(e, t) {
        if (!scope.startdrag) scope.stopsave++;
        scope.startdrag = 1;
      },
      drag: function(e, t) {
        t.helper.width("100%");
        
        t.helper.css({
          display: "inline-block",
          height: "auto"
        });
        // t.ihelper.float('left');
        // }
      },
      stop: function(e, t) {
        $(".canvas .column").sortable({
          opacity: 0.35,
          connectWith: ".column",
          start: function(e, t) {
            if (!scope.startdrag) scope.stopsave++;
            scope.startdrag = 1;
          },
          stop: function(e, t) {
            scope.setVueDom(t.item, true);
            scope.saveLayout();
            // scope.loadVue(wrapper, true);
            if (scope.stopsave > 0) scope.stopsave--;
            scope.startdrag = 0;
          }
        });
        if (scope.stopsave > 0) scope.stopsave--;
        scope.startdrag = 0;
      }
    });

    this.removeElm();
    this.gridSystemGenerator();
    this.initAddColButtonEvent();

    document.onkeydown = function(e) {
      var keyCode = e.keyCode || e.which || e.charCode;
      var ctrlKey = e.ctrlKey || e.metaKey;
      var shiftKey = e.shiftKey || e.metaKey;
      if (ctrlKey && keyCode == 83) {
        scope.saveHTML();
        e.preventDefault();
        return false;
      }
      if (ctrlKey && !shiftKey && keyCode == 90) {
        scope.undo();
        e.preventDefault();
        return false;
      }
      if (ctrlKey && shiftKey && keyCode == 90) {
        scope.redo();
        e.preventDefault();
        return false;
      }
    };
    $('body').off('click', '.el-icon-setting').on('click', '.el-icon-setting', function(event){
      /* var $tagName = $('#set_properties').attr('name') ;
      if($tagName){

      } */
      scope.selectTag.id = $(event.currentTarget).parents('.lyrow.ui-draggable').first().find('.view>div>div').children().first().attr('id');
      scope.selectTag.tagName = scope.selectTag.id && scope.selectTag.id.split('-')[1] ;
      $('#set_properties').attr({'targetid': scope.selectTag.id, 'name': scope.selectTag.tagName});
      scope.property = scope.properties.basic.concat(scope.properties[scope.selectTag.tagName] || []);
      // var id = $(this).parents('.configuration').first().siblings('.view').next().next().next().attr('id') ;
      if($('#set_properties').hasClass('hide')){
        // $('#set_properties').toggleClass('hide') ;
        $('#set_properties').removeClass('hide') ;
        $('.container-fix').css('margin-right', '200px');
      }
    }).on('input', '.inputprop', function(){
      var key = $.trim($(this).parent().prev().children('label').attr('name')) ;
      var value = $(this).val() ;
    // console.log('key:'+key+ '         value: ' + value ) ;
      var id = $('#set_properties').attr('targetid') ;
      scope.inputprop(id, key, value);
    }); 
    
    $(window).on('scroll', () => {
      scope.initContainer();
    });
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.container-fix {
  margin-left: 200px;
  padding-right: 15px;
  padding-left: 15px;
  margin-right: auto;
  height: fill-available;
}
.sidebar-nav {
  text-align: left !important;
  border: 1px solid #333;
 
}
.sidebar-nav.left{
  box-shadow: 5px 10px 5px #888888;
}
.sidebar-nav-right {
  left: inherit;
  right: 0;
  overflow: auto;
  box-shadow: -5px 10px 5px #888888;
}
.operate-menu {
  display: block !important;
}
</style>
