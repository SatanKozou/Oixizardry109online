省略形対応 ＆ 自動判定のハイブリッド型コードconfig/initializers/generator_default_type.rb の中身を以下のようにアップデートしてください。

# config/initializers/generator_default_type.rb
require "rails/generators/generated_attribute"

module Rails
  module Generators
    class GeneratedAttribute
      class << self
        # 1. 省略形のエイリアスマップを定義
        TYPE_ALIASES = {
          "s"  => "string",
          "i"  => "integer",
          "j"  => "json",
          "dt" => "datetime",
          "t"  => "text",
          "b"  => "boolean",
          "f"  => "float"
        }.freeze

        def parse(column_definition)
          name, type = column_definition.split(":")
          
          if type.present?
            # 2. 型が指定されている場合は、省略形（エイリアス）があれば変換する
            type = TYPE_ALIASES[type] || type
          else
            # 3. 型が省略されている場合は、名前から自動判定（ブレース展開と併用可能）
            type = case name
                   when "name", "title"
                     "string"
                   when "race", "sex", "alignment", "cclass", "level", "exp", "gold", "hp", "hp_max"
                     "integer"
                   else
                     "string" # どれにも当てはまらない場合のデフォルト
                   end
          end
          
          new(name, type)
        end
      end
    end
  end
end
