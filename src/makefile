# この makefile は、msys や cygwin などの Unix 互換環境上で利用することを想定している。
# ビルドには xdev68k が必要。
# https://github.com/yosshin4004/xdev68k

# 必要な環境変数が定義されていることを確認する。
ifndef XDEV68K_DIR
	$(error ERROR : XDEV68K_DIR is not defined.)
endif

# デフォルトサフィックスを削除
.SUFFIXES:

# 各種コマンド短縮名
RUN68 = ${XDEV68K_DIR}/run68/run68
HAS = $(RUN68) ${XDEV68K_DIR}/x68k_bin/HAS060.X
HLK = $(RUN68) ${XDEV68K_DIR}/x68k_bin/hlk301.x
CV = $(RUN68) ${XDEV68K_DIR}/x68k_bin/CV.X
RSIZE = $(RUN68) ${XDEV68K_DIR}/x68k_bin/RSIZE.R

# プログラム名(拡張子除く)
PROG = ../SXPKEY

# ヘッダ検索パス
INCLUDE_FLAGS = -I${XDEV68K_DIR}/include/xc

# *.HAS ソースファイル
ASM_SRCS = SXPKEY.HAS PCM.HAS FILE.HAS

# 中間ファイル生成用ディレクトリ
INTERMEDIATE_DIR = _build

# オブジェクトファイル
OBJS =	$(addprefix $(INTERMEDIATE_DIR)/,$(patsubst %.HAS,%.o,$(ASM_SRCS)))

# HLK に入力するリンクリスト
HLK_LINK_LIST = $(INTERMEDIATE_DIR)/_lk_list.tmp

# デフォルトのターゲット
all : $(PROG).R

# 中間生成物の削除
clean : 
	rm -f $(PROG).R
	rm -rf $(INTERMEDIATE_DIR)

# 実行ファイルの生成
#	HLK に長いパス文字を与えることは難しい。
#	回避策としてリンク対象ファイルを $(INTERMEDIATE_DIR) 以下にコピーし、
#	短い相対パスを用いてリンクを実行させる。
$(PROG).R : $(OBJS)
	mkdir -p $(INTERMEDIATE_DIR)
	rm -f $(HLK_LINK_LIST)
	@for FILENAME in $(OBJS); do\
		echo $$FILENAME >> $(HLK_LINK_LIST); \
        done
	$(HLK) -i $(HLK_LINK_LIST) -o $(PROG).X
	$(CV) $(PROG).X
	$(RSIZE) $(PROG).R
	mv $(PROG).r $(PROG).R
	rm -f $(PROG).X

# *.HAS ソースのアセンブル
$(INTERMEDIATE_DIR)/%.o : %.HAS makefile
	mkdir -p $(INTERMEDIATE_DIR)
	$(HAS) -e -u -w0 $(INCLUDE_FLAGS) $*.HAS -o $(INTERMEDIATE_DIR)/$*.o

$(INTERMEDIATE_DIR)/SXPKEY.o:	SXPKEY.HAS SXPKEY.MAC
$(INTERMEDIATE_DIR)/PCM.o:	PCM.HAS
$(INTERMEDIATE_DIR)/FILE.o:	FILE.HAS
